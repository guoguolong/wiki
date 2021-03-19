我们知道php本身就有一些系统预定义变量，通过这些变量可以简单的判断服务器操作系统是windows还是Lunix，在Mac OS执行下面代码

```php
echo php_uname() . PHP_EOL;
echo PHP_OS . PHP_EOL;
echo DIRECTORY_SEPARATOR . PHP_EOL;
echo PHP_SHLIB_SUFFIX . PHP_EOL;
echo PATH_SEPARATOR . PHP_EOL;
```

运行结果

```
Darwin bitbear.local 15.5.0 Darwin Kernel Version 15.5.0: Tue Apr 19 18:36:36 PDT 2016; root:xnu-3248.50.21~8/RELEASE_X86_64 x86_64
Darwin
/
so
:
```

php判断windows、Linux系统方法一：

```php
<?php
if(PATH_SEPARATOR==':'){
    echo 'Linux系统';
} else {
    echo 'Windows系统';
}
```

关于PATH_SEPARATOR 介绍：include 多个路径使用，在win下，当你要include多个路径的话，你要用”; ” 隔开，但在linux下就使用”: ”隔开的。这2个常量的使用能够避免不同平台的兼容性问题。

php判断windows还是Linux系统方法二：

```php
<?php
if (strtoupper(substr(PHP_OS, 0, 3)) === 'WIN') {
    echo '这个服务器操作系统为Windows!';
} else {
    echo '这个服务器操作系统不是Windows系统!';
}
```