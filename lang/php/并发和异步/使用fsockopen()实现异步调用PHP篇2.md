访问http://www.abc.com/gateway.php?url=<url> ,url是目标执行php文件，只是通过gateway.php来实现转发。

```php
// gateway.php
<?php
$data["channel"]=1;
$data["mobile"]=2;
$data["gateway"]=3;
$data["isp"]=4;
$data["linkid"]=5;
$data["msg"]=6;

$post = http_build_query($data); //模拟post数据.
$len = strlen($post);
//发送
$host = "localhost";
if(!empty($_GET['host'])) {    
    $host = $_GET['host'];
}
$path = $_GET['url']
$fp = fsockopen( $host , 80, $errno, $errstr, 30);
if (!$fp) {
    echo "$errstr ($errno)\n";
} else {
    
    $out = "POST $path HTTP/1.1\r\n";
    $out .= "Host: $host\r\n";
    $out .= "Content-type: application/x-www-form-urlencoded\r\n";
    $out .= "Connection: Close\r\n";
    $out .= "Content-Length: $len\r\n";
    $out .= "\r\n";
    $out .= $post."\r\n";
    // echo($out);
    fwrite($fp, $out);
    
    //实现异步把下面去掉
    // $receive = '';
    // while (!feof($fp)) {
        // $receive .= fgets($fp, 128);
    // }
    // echo "<br />".$receive;
    //实现异步把上面去掉
    
    fclose($fp);
}
```