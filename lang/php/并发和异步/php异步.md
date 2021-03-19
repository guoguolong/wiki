# PHP异步执行的常用方式常见的有以下几种，可以根据各自优缺点进行选择：

\##1.客户端页面采用AJAX技术请求服务器

优点：最简单，也最快，就是在返回给客户端的HTML代码中，嵌入AJAX调用，或者，嵌入一个img标签，src指向要执行的耗时脚本。 缺点：一般来说Ajax都应该在onLoad以后触发，也就是说，用户点开页面后，就关闭，那就不会触发我们的后台脚本了。

而使用img标签的话，这种方式不能称为严格意义上的异步执行。用户浏览器会长时间等待php脚本的执行完成，也就是用户浏览器的状态栏一直显示还在load。

当然，还可以使用其他的类似原理的方法，比如script标签等等。

## 2.popen()函数

该函数打开一个指向进程的管道，该进程由派生给定的 command 命令执行而产生。打开一个指向进程的管道，该进程由派生给定的 command 命令执行而产生。 所以可以通过调用它，但忽略它的输出。使用代码如下： pclose(popen("/home/xinchen/backend.php &", 'r'));

优点：避免了第一个方法的缺点，并且也很快。 缺点：这种方法不能通过HTTP协议请求另外的一个WebService，只能执行本地的脚本文件。并且只能单向打开，无法穿大量参数给被调用脚本。并且如果，访问量很高的时候，会产生大量的进程。如果使用到了外部资源，还要自己考虑竞争。

## 3.CURL扩展

CURL是一个强大的HTTP命令行工具，可以模拟POST/GET等HTTP请求，然后得到和提取数据，显示在"标准输出"（stdout）上面。代码如下：

```
$ch = curl_init();

$curl_opt = array(CURLOPT_URL, 'http://www.example.com/backend.php',
                            CURLOPT_RETURNTRANSFER, 1,
                            CURLOPT_TIMEOUT, 1,);
 curl_setopt_array($ch, $curl_opt);
 curl_exec($ch);
 curl_close($ch);
```

缺点：如你问题中描述的一样，由于使用CURL需要设置CUROPT_TIMEOUT为1（最小为1，郁闷）。也就是说，客户端至少必须等待1秒钟。

## 4.fscokopen()函数

fsockopen支持socket编程，可以使用fsockopen实现邮件发送等socket程序等等,使用fcockopen需要自己手动拼接出header部分 可以参考: http://cn.php.net/fsockopen/ 使用示例如下：

```
$fp = fsockopen("www.34ways.com", 80, $errno, $errstr, 30);

if (!$fp) {
    echo "$errstr ($errno)&lt;br /&gt;\n";
} else {
    $out = "GET /index.php  / HTTP/1.1\r\n";
    $out .= "Host: www.34ways.com\r\n";
    $out .= "Connection: Close\r\n\r\n";
  
    fwrite($fp, $out);
    /*忽略执行结果
    while (!feof($fp)) {
        echo fgets($fp, 128);
    }*/
    fclose($fp);
}
```

所以总结来说，fscokopen()函数应该可以满足您的要求。可以尝试一下。