php隐藏头部版本输出(X-Powered-By)信息的方法

在php程序中，默认会输出header信息：


    (Status-Line) HTTP/1.1 200 OK
    Date Wed, 23 Feb 2011 02:30:46 GMT
    Server Apache/2.2.13 (Win32)
    X-Powered-By: PHP/5.2.3
    Content-Length 30
    Keep-Alive timeout=5, max=100
    Connection Keep-Alive
    Content-Type text/html




要避免输出以上版本信息，可以修改php.ini 的expose_php把默认的On改成Off即可。

隐藏和伪装Apache的版本，隐藏php头部版本的话，可以参考如下的方法。

### 1，在Apache 的http.conf中添加：
ServerSignature Off
ServerTokens Prod

其中，ServerSignature Off告诉Apache在错误页(HTTP Status  404之类)不显示服务器版本信息，但此选项不影响可正常访问的页面(HTTP Status 200之类)。正常访问网页的Server  Header里面依然有服务器版本信息。

ServerTokens Prod告诉Apache在服务器头信息中(Server Header)中只返回Apache，不返回服务器操作系统与Apache的版本信息。

### 2，隐藏php头部版本php.ini
expose_php = Off
隐藏PHP程序头部发出的：X-Powered-By: PHP/5.2.4类似的信息

在php.ini文件中设置：
expose_php = Off
可以避免输出：X-Powered-By: PHP/5.2.4 这样的版本信息。 