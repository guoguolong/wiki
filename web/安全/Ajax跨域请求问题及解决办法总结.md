##  1. 浏览器的同源策略及规避方法

目前，所有浏览器都实行同源政策。即协议、域名、端口都相同的URI称为"同源"。不同源的url之间：

* 无法读取cookie、localstorage、indexDB
* 无法获取DOM
* 无法发送ajax请求

**`document.domain`规避半同源网页间不能共享`cookie`：**

`cookie`是服务器写入浏览器的一段信息。同源网页之间才能共享`cookie`。一级域名相同，二级域名不同的网页之间允许通过设置`document.domain='一级域名'`的方式共享`cookie`。另外服务器在设置`cookie`的时候，指定`cookie`所属的域名为一级域名，这样的话不需要做任何操作.

**postMesage规避不同源网页之间不能共享数据**：

html5新增的跨文档通信API为window对象新增了一个方法`window.postMesage`允许跨窗口通信，无论这两个窗口是否同源。

postMessage 方法有两个参数，第一个为需要传递的数据，第二个为传送目标窗口地址。通`window.addEventListener('message',function(){})`监听window的message事件获取数据。message事件的event对象有三个属性：event.source =>发送消息的窗口  event.origin=>消息发向的网址  event.data=>消息内容。 

**window.name规避父子窗口之间不能共享数据：（父窗口打开iframe子窗口）**

浏览器窗口有`window.name`属性。这个属性的最大特点是，无论是否同源，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。

这种方法的优点是，`window.name`容量很大，可以放置非常长的字符串；缺点是必须监听子窗口`window.name`属性的变化，影响网页性能。     

## 2. Ajax请求跨域

同源政策规定，AJAX请求只能发给同源的网址，否则就报错。

除了架设服务器代理（浏览器请求同源服务器，再由后者请求外部服务），有三种方法规避这个限制：

* jsonp （利用script标签不受同源策略影响，只支持get方式，一般用jquery的jsonp封装，接口需在返回值的外面包回调函数）
* websocket（一种通信协议，不受同源策略影响）
* CORS（CORS是跨源资源分享（Cross-Origin Resource Sharing）的缩写。是跨源AJAX请求的根本解决方法。）

`CORS`需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于`IE10`。整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的 AJAX 通信没有差别，代码完全一样。

浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。

因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。

**常见后端配置方案**
### PHP后台配置

PHP后台得配置几乎是所有后台中最为简单的,遵循如下步骤即可:

* 第一步:配置Php 后台允许跨域

```php
<?php
header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, Accept');
//主要为跨域CORS配置的两大基本信息,Origin和headers
```
* 第二步:配置Apache web服务器跨域(httpd.conf中)

```xml
<Directory>
  AllowOverride none
  Require all denied
</Directory>
```
改为以下代码：

```xml
<Directory >
  Options FollowSymLinks
  AllowOverride none
  Order deny,allow
  Allow from all
</Directory>
```

### JAVA后台配置

JAVA后台配置只需要遵循如下步骤即可:

* 第一步:获取依赖jar包下载 cors-filter-1.7.jar, java-property-utils-1.9.jar 这两个库文件放到lib目录下。(放到对应项目的webcontent/WEB-INF/lib/下)
* 第二步:如果项目用了Maven构建的,请添加如下依赖到pom.xml中:(非maven请忽视)

```xml
<dependency>
  <groupId>com.thetransactioncompany</groupId>
  <artifactId>cors-filter</artifactId>
  <version>[ version ]</version>
</dependency>
```

其中版本应该是最新的稳定版本,CORS过滤器

* 第三步: 添加CORS配置到项目的Web.xml中( App/WEB-INF/web.xml)
```xml
<!-- 跨域配置-->  
  <filter>
    <!-- The CORS filter with parameters -->
    <filter-name>CORS</filter-name>
    <filter-class>com.thetransactioncompany.cors.CORSFilter</filter-class>    
     <!-- Note: All parameters are options, if omitted the CORS
     Filter will fall back to the respective default values.
     -->
    <init-param>
      <param-name>cors.allowGenericHttpRequests</param-name>
      <param-value>true</param-value>
    </init-param> 
    <init-param>
      <param-name>cors.allowOrigin</param-name>
      <param-value>*</param-value>
    </init-param>
    <init-param>
      <param-name>cors.allowSubdomains</param-name>
      <param-value>false</param-value>
    </init-param>
    <init-param>
      <param-name>cors.supportedMethods</param-name>
      <param-value>GET, HEAD, POST, OPTIONS</param-value>
    </init-param>    
    <init-param>
      <param-name>cors.supportedHeaders</param-name>
      <param-value>Accept, Origin, X-Requested-With, Content-Type, Last-Modified</param-value>
    </init-param>
    <init-param>
      <param-name>cors.exposedHeaders</param-name>
      <!--这里可以添加一些自己的暴露Headers  -->
      <param-value>X-Test-1, X-Test-2</param-value>
    </init-param>
    <init-param>
      <param-name>cors.supportsCredentials</param-name>
      <param-value>true</param-value>
    </init-param>
    <init-param>
      <param-name>cors.maxAge</param-name>
      <param-value>3600</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <!-- CORS Filter mapping -->
    <filter-name>CORS</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

请注意,以上配置文件请放到web.xml的前面,作为第一个filter存在(可以有多个filter的)

* 第四步:可能的安全模块配置错误(注意，某些框架中-譬如公司私人框架，有安全模块的，有时候这些安全模块配置会影响跨域配置，这时候可以先尝试关闭它们)


### Node.js后台配置(express框架)

Node.js的后台也相对来说比较简单就可以进行配置。只需用express如下配置:

```js
app.all('*', function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");
  res.header("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
  res.header("X-Powered-By", ' 3.2.1');
  
  / /这段仅仅为了方便返回json而已
  res.header("Content-Type", "application/json;");
  if(req.method == 'OPTIONS') {
    // 让options请求快速返回
    res.sendStatus(200);
  } else {
    next();
  }
});
```