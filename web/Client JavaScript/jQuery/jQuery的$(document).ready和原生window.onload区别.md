$(document).ready原声代码是：

```
document.addEventListener("DOMContentLoaded",function(){
  // ......
});
```

两者区别是：$(document).ready方法在DOM树加载完成后就会执行，而window.onload是在页面资源（比如图片和媒体资源，它们的加载速度远慢于DOM的加载速度）加载完成之后才执行。也就是说$(document).ready要比window.onload先执行。

```
document.addEventListener("DOMContentLoaded",function(){
    console.log("html loaded");
});
window.onload = function (argument) {
	console.error('css/js/image loaded.');
}
```