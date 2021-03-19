## /project/test/call.php代码如下：

```php
<?php
$output = [];
exec('/projects/test/run.php  > /dev/null 2>&1 &' , $output);
echo 'Done.';
```

## /projects/test/run.php 代码如下：

```php
#!/usr/bin/env php
<?php
$cnt = 0;
$data_file =  '/projects/test/data.txt';
while(true) {
  file_put_contents($data_file, $cnt . ':' . time());
  sleep(1);
  $cnt++;
  if($cnt &gt; 10) break;
}
file_put_contents($data_file, time() . '...done!');
```

运行 /project/test/call.php， exec函数异步执行，核心点在:

```bash
> /dev/null 2>&1 &
```

## > /dev/null 2>&1 & 的含义

```
可以简单的理解 /dev/null 时linux下的回收站 
>  默认时把标准输出重定向
2>&1 时把出错输出也定向到标准输出
```

具体解释：

null是一个名叫null小桶的东西，将输出重定向到它的好处是不会因为输出的内容过多而导致文件大小不断的增加。其实，你就认为null就是什么都没有，也就是，将命令的输出扔弃掉了。

1表示标准输出，2表示标准错误输出，2>&1表示将标准错误输出重定向到标准输出，这样，程序或者命令的正常输出和错误输出就可以在标准输出输出。

一般来讲标准输出和标准错误输出都是屏幕，那为什么还要这么用呢？原因是标准输出的重定向。你的例子是重定向到了null，如果重定向到文件，例如： dir > out.txt 表示标准输出重定向到out.txt文件。此时如果dir命令出错，那么错误信息不会输出到out.txt文件，错误信息仍然会输出到屏幕——标准错误输 出。为了使正确的信息和错误的信息都重定向到out.txt文件，那么需要将错误信息的标准错误输出重定向到标准输出。即命令如下： dir > out.txt 2>&1 重定向到null是一个道理。 dir > null 2>&1