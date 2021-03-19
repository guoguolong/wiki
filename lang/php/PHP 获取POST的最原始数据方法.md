PHP 获取POST的最原始数据方法

一般我们都用$_POST或$_REQUEST两个预定义变量来接收POST提交的数据。但如果提交的数据没有变量名，而是直接的字符串，则需要使用其他的方式来接收。

## 方法一： 使用全局变量$GLOBALS['HTTP_RAW_POST_DATA']来获取。

在$GLOBALS['HTTP_RAW_POST_DATA']存放的是POST过来的原始数据。而$_POST或$_REQUEST存放的是 PHP以key=>value的形式格式化以后的数据。 但$GLOBALS['HTTP_RAW_POST_DATA']中是否保存POST过来的数据取决于centent-Type的设置，即POST数据时 必须显式示指明Content-Type: application/x-www-form-urlencoded，POST的数据才会存放到 $GLOBALS['HTTP_RAW_POST_DATA']中。

## 方法二： 使用file_get_contents(“php://input”)来获取。

对于未指定 Content-Type 的POST数据，则可以使用file_get_contents(“php://input”);来获取原始数据。

事实上，用PHP接收POST的任何数据都可以使用本方法。而不用考虑Content-Type,包括二进制文件流也可以。

所以用方法二是最保险的方法。