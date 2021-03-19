### 1. set_error_handler() 

set_error_handler()执行完后，代码可以继续执行，除非函数尾部增加了die()之类的函数. 可以捕获trigger_error发出的错误，但是set_error_handler不是万能的， E_ERROR、E_PARSE、E_CORE_ERROR、E_CORE_WARNING、E_COMPILE_ERROR、E_COMPILE_WARNING 是不能被捕获的。Fatal Error显然是不能捕获的

### 2. register_shutdown_function()

该函数被总是被调用，所以Fatal Error这类错误会被其拦截. 一般配合error_get_last

```php
$e = error_get_last();
    if(!empty($e)) {
    // 处理一些事情
    print_r($e);
}
```

### 3. 闭包

```php
<?php

function f() {
    $b = 6;
    $g = function(&amp;$b) use(&amp;$b) {
        ++$b;
        return $b;
    };
    return $g;
}
$g = f();

echo $g() . PHP_EOL;

echo $g() . PHP_EOL;
```