Yaf官网：http://yaf.laruence.com/

Yaf 一些扩展包：https://github.com/sillydong/CZD_Yaf_Extension

Yaf

yaf.c 80行

```
STD_PHP_INI_ENTRY("yaf.environ",             "product", PHP_INI_SYSTEM, OnUpdateString, environ, zend_yaf_globals, yaf_globals)
```

修正为

```
STD_PHP_INI_ENTRY("yaf.environ",             "product", PHP_INI_ALL, OnUpdateString, environ, zend_yaf_globals, yaf_globals)
```