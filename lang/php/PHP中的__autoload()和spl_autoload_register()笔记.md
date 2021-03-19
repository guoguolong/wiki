在看框架源码时，发现了__autoload和apl_autoload_register这两个函数，于是对其进行了一番学习。

php的__autoload函数是一个魔术函数，在这个函数出现之前，如果一个php文件里引用了100个对象，那么这个文件就需要使用include或require引进100个类文件，这将导致该php文件无比庞大。于是就有了这个 __autoload函数。

__autoload函数在什么时候调用呢？当php文件中使用了new关键字实例化一个对象时，如果该类没有在本php文件中被定义，将会触发__autoload函数，此时，就可以引进定义该类的php文件，尔后，就能实例化成功了。（注意：如果需要实例化的对象，在本文件中已经找到该类的定义的话，就不会触发__autoload函数）

如：  
```php
#Animal.php
<?php
  class Animal{}
?>

#main.php
<?php
  function __autoload($classname){
    $classpath = "{$classname}.php";
    if(file_exists($classpath)){
        require_once($classpath);
    }else{
        echo $classpath." not be found!";
    }
  }
  $ani = new Animal();
```

如上述两个文件，运行php main.php

（1）运行到new Animal()时，发现 class Animal没有定义;  
（2）触发了__autoload函数，该函数引进了Animal.php文件；  
（3）实例化成功。

好了，了解完了__autoload函数的作用，再来看看spl_autoload_register函数的作用。

spl_autoload_register函数的作用就是将自定义函数设置替换为__autoload函数（注意：当文件中同时出现__autoload和spl_autoload_register时，以spl_autoload_register为准）

那么将main.php改成如下也有同样的作用:

```php
#main.php
<?php
  function myLoad($classname){
    $classpath = "{$classname}.php";
    if(file_exists($classpath)){
        require_once($classpath);
    }else{
        echo $classpath." not be found!";
    }
  }
  spl_autoload_register("myLoad");
  $ani = new Animal();
?>
```
附官方定义：http://www.php.net/manual/zh/function.spl-autoload-register.php

```php
bool spl_autoload_register ([ callable $autoload_function [, bool $throw = true [, bool $prepend = false ]]] )
```

参数
* autoload_function
欲注册的自动装载函数。如果没有提供任何参数，则自动注册 autoload 的默认实现函数spl_autoload()。

* throw  
此参数设置了 autoload_function 无法成功注册时， spl_autoload_register()是否抛出异常。

* prepend
如果是 true，spl_autoload_register() 会添加函数到队列之首，而不是队列尾部。  
将函数注册到SPL __autoload函数队列中。如果该队列中的函数尚未激活，则激活它们。

如果在你的程序中已经实现了__autoload()函数，它必须显式注册到__autoload()队列中。因为 spl_autoload_register()函数会将Zend Engine中的__autoload()函数取代为spl_autoload()或spl_autoload_call()。

如果需要多条 autoload 函数，spl_autoload_register() 满足了此类需求。 它实际上创建了 autoload 函数的队列，按定义时的顺序逐个执行。相比之下， __autoload() 只可以定义一次。 
