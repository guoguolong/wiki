内容均以php5.6.14为例.

## 一. 封装函数时产生 memory leaks.

```
[weichen@localhost www]$ php 2.php 
[122,3333]
[Tue Jul 10 15:34:42 2016]  Script:  '/home/www/2.php'
/home/weichen/Downloads/pdoner/pdoner.c(83) :  Freeing 0x7F86B52F79F8 (32 bytes), script=/home/www/2.php
[Tue Jul 10 15:34:42 2016]  Script:  '/home/www/2.php'
/home/weichen/Downloads/php-5.6.14/ext/standard/string.c(1161) :  Freeing 0x7F86B52F7B60 (79 bytes), script=/home/www/2.php
```

=== Total 2 memory leaks detected ===

php编译开启 --enable-debug，如果扩展中存在内存泄漏，会有相应提示。内存泄漏问题相当困扰。 为什么会有内存泄露？是你的函数一直在申请内存做某件事，而功能完成后没有释放内存。

网上的 hello world 程序很多，基本是不讲内存处理的，即便稍作修改，也无法用于真实项目。 所以释放内存也是底层程序的关键点。现在分析一下上面的提示信息，总共检测有 2 处内存泄漏。

第(1)处： [Tue Jul 10 15:34:42 2016] Script: '/home/www/2.php' /home/weichen/Downloads/pdoner/pdoner.c(83) : Freeing 0x7F86B52F79F8 (32 bytes), script=/home/www/2.php

提示我们 pdoner.c(83) 有问题，回到程序中是 MAKE_STD_ZVAL(glue); 给 glue 初始化没问题，问题是用完了没有释放，很容易想到的是要释放掉 glue。 zval_ptr_dtor(&glue);

编译安装依旧有问题，出现段错误一般是指针使用有误：

```
[weichen@localhost www]$ php 2.php 
[Tue Jul 10 14:58:25 2016]  Script:  '/home/www/2.php'
---------------------------------------
/home/weichen/Downloads/php-5.6.14/Zend/zend_execute.h(79) : Block 0x7fb3f93459c8 status:
/home/weichen/Downloads/php-5.6.14/Zend/zend_variables.c(37) : Actual location (location was relayed)
Invalid pointer: ((thread_id=0x007A7C7A) != (expected=0x06567840))
Segmentation fault (core dumped)
```

zval_ptr_dtor 使用有什么讲究？这时候最好先查阅一下内核中的用法。

zend_API.h 中有这样一处用法：

```c
#define ZVAL_ZVAL(z, zv, copy, dtor) do {       \
        zval *__z = (z);                        \
        zval *__zv = (zv);                      \
        ZVAL_COPY_VALUE(__z, __zv);             \
        if (copy) {                             \
            zval_copy_ctor(__z);                \
        }                                       \
        if (dtor) {                             \
            if (!copy) {                        \
                ZVAL_NULL(__zv);                \
            }                                   \
            zval_ptr_dtor(&__zv);               \
        }                                       \
    } while (0)
```

注意，在调用 zval_ptr_dtor 销毁 __zv 之前，调用了 ZVAL_NULL(__zv) 把指针置为null。

所以我们照这种方式，把 glue 设为null。 ZVAL_NULL(glue); zval_ptr_dtor(&glue); 编译运行，可以看到只剩一处提示了。

第(2)处： [Tue Jul 10 15:34:42 2016] Script: '/home/www/2.php' /home/weichen/Downloads/php-5.6.14/ext/standard/string.c(1161) : Freeing 0x7F86B52F7B60 (79 bytes), script=/home/www/2.php 当时这个还没有解决掉，先继续往下看，回头再一起看这个的解决办法。

## 二. 封装类时产生 memory leaks.

PDONER_ERRS_* 均为定义的常量，下面是无内存泄漏的版本：

```c
/* {{{ proto public Errs::__construct(void) */
PHP_METHOD(errs, __construct)
{
    zval *msg;
    MAKE_STD_ZVAL(msg);
    array_init(msg);

    add_index_string(msg, PDONER_ERRS_SUCC, "成功", 1);
    add_index_string(msg, PDONER_ERRS_FAIL, "失败", 1);
    add_index_string(msg, PDONER_ERRS_EXCEP, "异常", 1);
    add_index_string(msg, PDONER_ERRS_UNKNOW, "未知", 1);

    zend_update_property(errs_ce, getThis(), ZEND_STRL(PDONER_ERRS_PROPERTY_NAME_MSG), msg TSRMLS_CC);

    zval_ptr_dtor(&msg);

/*
    add_index_string(msg, PDONER_ERRS_SUCC, "成功", 0);
    add_index_string(msg, PDONER_ERRS_FAIL, "失败", 0);
    add_index_string(msg, PDONER_ERRS_EXCEP, "异常", 0);
    add_index_string(msg, PDONER_ERRS_UNKNOW, "未知", 0);

    add_property_zval_ex(getThis(), PDONER_ERRS_PROPERTY_NAME_MSG, sizeof(PDONER_ERRS_PROPERTY_NAME_MSG), msg TSRMLS_CC);
*/
}
/* }}} */
```

扩展类的属性无法直接初始化成数组和对象，所以只能以修改属性的方式操作，在构造函数内 或者 MINIT 阶段。 注意，由于属性赋值在 construct 阶段，如果没有实例化类，扩展内部 zend_read_property 时还是没有值的。

zend_update_property 第二个参数是当前对象，所以没有办法用在 MINIT 阶段，上面用法参考了 Yaf-2.3.5：

```c
PHP_METHOD(yaf_config_ini, __construct) {
    zval *filename, *section = NULL;

    if (zend_parse_parameters(ZEND_NUM_ARGS() TSRMLS_CC, "z|z", &filename, &section) == FAILURE) {
        zval *prop;
        MAKE_STD_ZVAL(prop);
        array_init(prop);
        zend_update_property(yaf_config_ini_ce, getThis(), ZEND_STRL(YAF_CONFIG_PROPERT_NAME), prop TSRMLS_CC);
        zval_ptr_dtor(&prop);
        return;
    }

    (void)yaf_config_ini_instance(getThis(), filename, section TSRMLS_CC);
}
```

非理想情况下：

1). 我们的构造函数中，如果传了非 duplicate 的参数：add_index_string(msg, PDONER_ERRS_SUCC, "成功", 0); 调用 Errs::$msg 可以成功输出内容，但是有段错误：

```
[Thu Jul 10 17:04:38 2016] Script: '/home/www/2.php'
---------------------------------------
/home/weichen/Downloads/php-5.6.14/Zend/zend_execute.h(79) : Block 0x7fbf5ed4bb4e status:
/home/weichen/Downloads/php-5.6.14/Zend/zend_variables.c(37) : Actual location (location was relayed)
Invalid pointer: ((thread_id=0x646F6C70) != (expected=0x6BF6E840))
Segmentation fault (core dumped)
```

Reference：when to duplicate string using add_index_string? http://grokbase.com/t/php/php-dev/01285y74sp/when-to-duplicate-string-using-add-index-string 有其它地方用，则复制一份出去；没有其它地方用，duplicate 填 0 .

2). 启用注释段代码的情况，可以成功输出，却有 11 处内存泄漏：

```
[Thu Jul 10 17:06:56 2016] Script: '/home/www/2.php'
/home/weichen/Downloads/php-5.6.14/Zend/zend_API.c(1369) : Freeing 0x7F1900D44820 (72 bytes), script=/home/www/2.php
/home/weichen/Downloads/php-5.6.14/Zend/zend_hash.c(419) : Actual location (location was relayed)
Last leak repeated 3 times
[Thu Jul 10 17:06:56 2016] Script: '/home/www/2.php'
/home/weichen/Downloads/php-5.6.14/Zend/zend_API.c(1366) : Freeing 0x7F1900D448C8 (32 bytes), script=/home/www/2.php
Last leak repeated 3 times
[Thu Jul 10 17:06:56 2016] Script: '/home/www/2.php'
/home/weichen/Downloads/pdoner/pdoner.c(120) : Freeing 0x7F1900D45C58 (32 bytes), script=/home/www/2.php
[Thu Jul 10 17:06:56 2016] Script: '/home/www/2.php'
/home/weichen/Downloads/php-5.6.14/Zend/zend_hash.c(392) : Freeing 0x7F1900D45D58 (64 bytes), script=/home/www/2.php
/home/weichen/Downloads/php-5.6.14/Zend/zend_alloc.c(2583) : Actual location (location was relayed)
[Thu Jul 10 17:06:56 2016] Script: '/home/www/2.php'
/home/weichen/Downloads/pdoner/pdoner.c(121) : Freeing 0x7F1900D467C0 (72 bytes), script=/home/www/2.php
/home/weichen/Downloads/php-5.6.14/Zend/zend_API.c(1011) : Actual location (location was relayed)
=== Total 11 memory leaks detected ===
```

可见是没有销毁 zval *msg 的缘故。你加上 zval_ptr_dtor(&msg); 又会报段错误。 注意上面的用法没有这样用：add_property_zval_ex(getThis(), ZEND_STRL(PDONER_ERRS_PROPERTY_NAME_MSG), msg TSRMLS_CC);

## 三. 字符串返回值的难题.

回到第(2)处的字符串问题上，刚开始是这么做的： 复制代码 char *src1 = "["; char *ori = Z_STRVAL_P(return_value); char *src2 = "]";

```c
char *dest = (char *)emalloc(1024);

strcat(dest, src1);
strcat(dest, ori);
strcat(dest, src2);

RETURN_STRING(dest, 0);
```

复制代码 显然 dest 是长期占用内存的，但你如何在返回值之后，还能再把它销毁呢，恐怕无法做到。 这里就要引入一个概念，当你的函数没有返回值时，函数默认返回的变量是 zval *return_value，也就是你用它就不会有问题。 另外，我们用内核中提供的字符串连接函数 concat_function(zval *result, zval *op1, zval *op2) 代替 strcat 更有效的处理。 concat_function 在 ./Zend/zend_operators.c:1422，有时候不清楚用法最好是看它的实现。

使用 concat_function 之后，有一些要注意的问题，看代码：

```c
// 第一种方法，引入一个变量    zval result;

concat_function(&result, &op1, return_value TSRMLS_CC);
concat_function(&result, &result, &op2 TSRMLS_CC);

// 1. zval_ptr_dtor(&return_value) was wrong.
// 2. forget zval_dtor(return_value) will cause memory leaks.
zval_dtor(return_value);

// copy result to return_value;
// if "zval result" is not zero-terminated, use ZVAL_ZVAL() instead, like the way 2. (PHP Warning:  String is not zero-terminated.)
ZVAL_COPY_VALUE(return_value, &result);
   
// 第二种方法，更简洁
concat_function(&op1, &op1, return_value TSRMLS_CC);
concat_function(&op1, &op1, &op2 TSRMLS_CC);
zval_dtor(return_value);
ZVAL_ZVAL(return_value, &op1, 0, 1); 

zval_dtor(&op1);
zval_dtor(&op2);
```

上面是 pdoner 扩展函数 pd_implode_json 的实现：https://github.com/farwish/pdoner

php 未开启 debug 模式情况下使用 valgrind 工具：http://tina.reeze.cn/book/?p=chapt06/06-07-memory-leaks