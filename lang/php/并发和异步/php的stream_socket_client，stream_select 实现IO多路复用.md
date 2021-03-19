## 问题

假设你正在像多个服务器请求服务，理想的情况时同时向多台服务器发送请求，而不是一台接一台。可以实现吗？

## 回答：

可以！不过当有人想要实现并发功能时，他们通常会想到用fork或者spawn threads、pthread(http://pecl.php.net/package/pthreads)，这里我们介绍的是一个另一种技术：I/O多路复用

假设现在要建立一个服务来检查正在运行的n台服务器，以确定他们还在正常运转。你可能会写下面这样的代码：

```php
<?php
$hosts = array("host1.sample.com", "host2.sample.com", "host3.sample.com");
$timeout = 15;
$status = array();
foreach ($hosts as $host) {
    $errno = 0;
    $errstr = "";
    $s = fsockopen($host, 80, $errno, $errstr, $timeout);
    if ($s) {
        $status[$host] = "Connectedn";
        fwrite($s, "HEAD / HTTP/1.0rnHost: $hostrnrn");
        do {
            $data = fread($s, 8192);
            if (strlen($data) == 0) {
                break;
            }
            $status[$host] .= $data;
        } while (true);
        fclose($s);
    } else {
        $status[$host] = "Connection failed: $errno $errstrn";
    }
}
print_r($status);
```

我们可以建立异步连接（不需要等待fsockopen返回连接状态，PHP仍然需要解析hostname对应的IP，除非你直接使用IP），不过在打开一个连接之后立刻返回，继而我们就可以连接下一台服务器。

有两种方法可以实现:

1. PHP5中可以使用新增的stream_socket_client()函数直接替换掉fsocketopen()。 ====

```php
<?php 
$hosts   = ["achilles.tucower.me", "ares.tucower.me"];
$timeout = 15;
$status  = [];
$sockets = [];
foreach ($hosts as $id => $host) {
    $s = stream_socket_client("$host:80", $errno, $errstr, $timeout, STREAM_CLIENT_ASYNC_CONNECT|STREAM_CLIENT_CONNECT);
    if ($s) {
        $sockets[$id] = $s;
        $status[$id] = "in progress";
    } else {
        $status[$id] = "failed, $errno $errstr";
    }
}

while (count($sockets)) { // 循环的退出是满足读数据strlen(data)==0 ，然后将sockets数组递减达成的。
    $read = $write = $sockets;
    $e = null;
    $n = stream_select($read, $write, $e, $timeout);
    if ($n > 0) {
        foreach ($read as $r) {
            $id = array_search($r, $sockets);
            $data = fread($r, 8192);
            if (strlen($data) == 0) {
                if ($status[$id] == "in progress") {
                    $status[$id] = "failed to connect"; 
                }
                fclose($r);
                unset($sockets[$id]);
            } else {
                $status[$id] .= $data;
            }
        }
        foreach ($write as $w) {
            if (count($sockets) > 0) {
                $id = array_search($w, $sockets);
                fwrite($w, "HEAD / HTTP/1.1\r\nHost: " . $hosts[$id] . "\r\n\r\n");
                $status[$id] = "waiting for response";
            }
        }
    } else {
        foreach ($sockets as $id => $s) {
            $status[$id] = "timed out " . $status[$id];
        }
        break;
    } 
}
foreach ($hosts as $id => $host) { 
    echo "Host: $host\n";
    echo "Status: " . $status[$id] . "\n\n";
}
```

我们用stream_select()等待sockets打开的连接事件。stream_select()调用系统的select(2)函数来工作：前面三个参数是你要使用的streams的数组；你可以对其读取，写入和获取异常（分别针对三个参数）。stream_select()可以通过设置$timeout（秒）参数来等待事件发生-事件发生时，相应的sockets数据将写入你传入的参数。

1. PHP4.1.0之后(PHP5之前)版本的实现 ==== 如果你已经在编译PHP时包含了sockets(ext/sockets)支持，你可以使用根上面类似的代码，只是需要将上面的streams/filesystem函数的功能用ext/sockets函数实现。主要的不同在于我们用下面的函数代替stream_socket_client()来建立连接：

```php
<?php 
// This value is correct for Linux, other systems have other values define('EINPROGRESS', 115); 
function non_blocking_connect($host, $port, &$errno, &$errstr, $timeout) {
    $ip = gethostbyname($host); $s = socket_create(AF_INET, SOCK_STREAM, 0);
    if (socket_set_nonblock($s)) {
        $r = @socket_connect($s, $ip, $port);
        if ($r || socket_last_error() == EINPROGRESS) {
            $errno = EINPROGRESS; return $s; 
        }
    }
    $errno = socket_last_error($s);
    $errstr = socket_strerror($errno);
    socket_close($s);
    return false; 
}
```

现在用socket_select()替换掉stream_select()，用socket_read()替换掉fread()，用socket_write()替换掉fwrite()，用socket_close()替换掉fclose()就可以执行脚本了！

PHP5的先进之处在于，你可以用stream_select()处理几乎所有的stream，例如你可以通过include STDIN用它接收键盘输入并保存进数组，你还可以接收通过proc_open()打开的管道中的数据。