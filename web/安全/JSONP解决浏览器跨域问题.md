[toc]

跨域问题起源于：[如果你在a.com/client.html通过JavaScript访问b.com/serve.php将会由于浏览器安全因素无法返回数据。](http://xn--a-376ay2w0qcz31a.com/client.html通过JavaScript访问b.com/serve.php将会由于浏览器安全因素无法返回数据。)

## 1. 跨域问题最直接解决方案

[资源提供方b.com/server.php设置允许跨域访问，一个可能的server.php的内容为：](http://xn--b-hc7ao06d6ybh8ro18b.com/server.php设置允许跨域访问，一个可能的server.php的内容为：)

```php
<?php
header("Access-Control-Allow-Origin: *");
echo 'Serve Data';
```

必须知道这种方案有浏览器兼容问题，即：IE9及以下版本IE不支持。

## 2. JSONP解决方案

jsonp解决方案的核心点是使用script这个html标签：

```
<script src="http://b.com/serve.php"></script>
```

因此要求b.com上的serve.php返回合法的js。

### 2.1. 被访问域b.com上的serve.php

一个可能的serve.php例子如下：

```
<?php
if(empty($_GET['callback'])) {
    $_GET['callback'] = 'default_call';
}
$callback = $_GET['callback'];
$data = [
    'username' => 'Allen Guo',
    'gender' => 1,
];
echo "$callback(". json_encode($data) .");";
```

运行http://b.com/say.php?callback=call1返回

```
call1({"username":"Allen Guo","gender":1});
```

### 2.2. 访问域a.com上的client.html

直接贴代码了

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <style>input { padding: 6px; }</style>
    </head>
    <body>
        <fieldset>
            <legend>跨域方法1</legend>
            <div>
                <input type="button" id="btn-get-json1" value="Get Json"/>

                <span id="result1"></span>
            </div>
        </fieldset>
        <fieldset>
            <legend>跨域方法2</legend>
            <div>
                <input type="button" id="btn-get-json2" value="Get Json"/>
                <span id="result2"></span>
            </div>
        </fieldset>
        <script src="http://libs.baidu.com/jquery/1.10.2/jquery.min.js"></script>
        <script>
            var domain2_url = 'http://b.com/serve.php';

            function call1(params) {
                $('#result1').html('Name is:' + params.username);
            }

            $(document).ready(function () {
                $('#btn-get-json1').on('click' , function() {
                    function create_script(src) {
                        $("<script><//script>").attr("src", src).appendTo("body")

                    }
                    create_script(domain2_url + "?callback=call1");
                });

                $('#btn-get-json2').on('click' , function() {
                   $.ajax({
                        url: domain2_url,
                        dataType: "jsonp",
                        jsonp: "callback",
                        success: function (data) {
                         $('#result2').html('Name is:' + data.username);
                            console.log(data);
                        }
                    })
                });
            });
        </script>
    </body>
</html>
```

代码示例中“跨域方法1”是基础方法，“跨域方法2”是jquery提供的便利API，只要指定dataType为jsonp,它就会发出<script src..这样的请求.

## 3. JSONP优缺点

JSONP的优点是：它不像XMLHttpRequest对象实现的Ajax请求那样受到同源策略的限制；它的兼容性更好，在更加古老的浏览器中都 可以运行，不需要XMLHttpRequest或ActiveX的支持；并且在请求完毕后可以通过调用callback的方式回传结果。

JSONP的缺点则是：它只支持GET请求而不支持POST等其它类型的HTTP请求；它只支持跨域HTTP请求这种情况，不能解决不同域的两个页面之间如何进行JavaScript调用的问题。