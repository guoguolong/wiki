DNS 实现域名到IP的映射。通过域名访问站点，每次请求都要做DNS解析。目前每次DNS解析，通常在200ms以下。针对DNS解析耗时问题，一些浏览器通过DNS Prefetch 来提高访问的流畅性。

DNS Prefetch 是一种DNS 预解析技术，当浏览网页时，浏览器会在加载网页时对网页中的域名进行解析缓存，这样在单击当前网页中的连接时就无需进行DNS的解析，减少用户等待时间，提高用户体验。

目前支持 DNS Prefetch 的浏览器有 google chrome 和 firefox 3.5

如果要浏览器端对特定的域名进行解析，可以再页面中添加link标签实现。例如：

```
<link rel="dns-prefetch" href="www.ytuwlg.iteye.com" />
```

如果要控制浏览器端是否对域名进行预解析，可以通过Http header 的x-dns-prefetch-control 属性进行控制。

可惜目前支持上面标签的只有 google chrome 和 firefox3.5