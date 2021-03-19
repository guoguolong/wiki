<script>标签有两个和脚本加载时机有关的属性：async和defer。按照规范，这两个属性只有在<script>标签中设置了src属性时才能起作用，因此，者两个属性不应该使用在内联脚本上（尽管很多浏览器支持）。

属性defer是HTML 4.01规范中定义的，它表明<script元素中包含的JavaScript代码并不会修改DOM结构，因此代码可以延迟执行。目前，defer属性得到了大部分主流浏览器的支持，但Opera Mini还不支持此属性。浏览器解析到设置了此属性的JavaScript引用，就会以并行的方式下载脚本，而不是阻塞的方式下载，脚本加载完成后，浏览器会在DOMContentLoaded触发之前按照引用顺序运行JavaScript代码。

属性async是HTML5规范中新定义的属性，它表明脚本以异步的方式加载和执行。属性async也得到了大部分主流浏览器的支持，目前只在IE9及以下版本和Opera Mini浏览器中不可用。浏览器会以异步方式下载JavaScript代码文件，并在下载结束后立即执行代码，并不会等待页面解析结束。

使用defer和async属性的示例代码如下
<!-- HTML4.01规范中定义了defer属性 -->
<script src="file.js" defer="defer"></script>
<!-- HTML5 规范中定义了async属性 -->
<script src="file.js" async="sync"></script>


很多开发者会混淆这两个属性，使用不当还会导致脚本错误，属性defer的作用是让脚本后置加载，相当于脚本放置于页面最后面加载和执行。属性async的作用是让脚本异步加载和执行。

两个属性差别是：设置async属性后不能保证脚本按照顺序加载和执行，脚本加载完成后会立即执行，而设置defer的脚本还是会按照原有的顺序执行。因此，如果脚本执行之间有依赖关系，则不能使用async属性；如果页面中有内敛的脚本依赖于加载的脚本，则不适合使用defer属性。

从功能上来说，可以使用async属性的场合也可以使用defer属性。因此，在设置async属性时，推荐同时设置defer属性，这样当浏览器不支持async属性时defer属性会起作用，从而最大限度地提高脚本加载执行的性能.

关于defer的一些补充：

既然defer表明<script>元素中包含的JavaScript代码并不会修改DOM结构，那么document.write就是不能调用的。另外既然浏览器会在DOMContentLoaded触发之前按照引用顺序运行JavaScript代码，那么可以做个对比
<script src="./js/file.js" defer="defer"></script>
<script src="./js/file.js" ></script>


将上述两行放在<head>里，如果页面中有id为username的标签，那么 file.js中执行 document.getElementById('username')，不带defer属性的脚本将返回undefined;带defer属性的则会返回正确的对象