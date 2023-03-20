[toc]

最近在研究csrf的时候发现了google发布的SameSite防御方法。

看了一下国内的资料，基本都是16-17年的了，就选择直接去看国外的文档了，所以本篇基本也相当于一个翻译稿子，不过会加入一点我个人的理解。

一直以来我们都知道cookie的五大属性:“path, domain, expire, HttpOnly, Secure”，很少有人了解到cookie还有一个SameSite属性，这是一个专门用于防止csrf漏洞的属性。

在cookie上引入SameSite属性提供了三种不同的方法来控制CSRF漏洞。您可以选择不指定属性，也可以使用Strict或Lax将cookie限制为同站点请求。

## 1. 设置SameSite = Strict，则表示您的cookie仅在第一方网站中发送使用。

通俗来讲，只有当Cookie的网站与浏览器的网址栏中当前显示的网站相匹配时，才会发送Cookie，也就是这个网站的cookie如果被设置成strict模式，那么这个cookie绝对不会交给其他网站作为cookie进行。

当用户在您的站点上时，cookie将按预期与请求一起发送。但是，当关联到您的网站的其他链接时，比如去另一个网站，在该初始请求中，将不会发送cookie。

## 2. 设置SameSite = Lax，当用户发送GET方法的同步请求时，将会发送cookie。

同步请求（可能改变当前页面，也可能打开新页面），比如通过对<a>的点击，对<form>的提交，还有改变 location.href，调用 window.open() 等方式产生的请求。POST提交表格的方式就不行。

## 3. 当SameSite = None时，这意味着你可以在第三方环境中发送cookie

![image](../images/samesite-cookies.png)

## 4. 该怎么做才能启用SameSite

大多数语言和库都支持Cookie的SameSite属性，但是添加SameSite = None仍然相对较新，这意味着您可能需要解决一些标准行为，这些在GitHub上的[SameSite示例](https://github.com/GoogleChromeLabs/samesite-examples)中记录。

## 5. 参考

https://web.dev/samesite-cookies-explained
https://www.lstazl.com/