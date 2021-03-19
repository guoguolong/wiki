## 1. window.onload不能同时编写多个，如果有多个window.onload方法，只会执行一个

$(document).ready()可以同时编写多个，并且都可以得到执行

## 2. 图片高度宽度计算

由于在 $(document).ready() 方法内注册的事件，只要 DOM 就绪就会被执行，因此可能此时元素的关联文件未下载完。例如与图片有关的 html 下载完毕，并且已经解析为 DOM 树了，但很有可能图片还没有加载完毕，所以例如图片的高度和宽度这样的属性此时不一定有效。要解决这个问题，可以使用 Jquery 中另一个关于页面加载的方法 ---load() 方法。 Load() 方法会在元素的 onload 事件中绑定一个处理函数。如果处理函数绑定给 window 对象，则会在所有内容 ( 包括窗口、框架、对象和图像等 ) 加载完毕后触发，如果处理函数绑定在元素上，则会在元素的内容加载完毕后触发。

```javascript
$(window).load(function (){
    // 编写代码
});

// 等价于 JavaScript 中的以下代码
window.onload = function (){
    // 编写代码
}
```

## 3. script脚本与onload/ready的关系

无论<script>内部、外部脚本放在何处（即使在onload/ready后棉），onload/ready 都是等待<script>标签下载、执行完成才执行.

## 4. iframe

最近在改一个嵌入在frame中的页面的时候，使用了jquery做效果，而页面本身也绑定了onload事件。改完后，Firefox下测试正常流畅，IE下就要等个十几秒jquery的效果才出现，黄花菜都凉了。

起初以为是和本身onload加载的方法冲突。网上普遍的说法是$(document).ready()是在页面DOM解析完成后执行，而onload事件是在所有资源都准备完成之后才执行，也就是说$(document).ready()是要在onload之前执行的，尤其当页面图片较大较多的时候，这个时间差可能更大。可是我这页面分明是图片都显示出来十几秒了，还不见jquery的效果出来。

删了onload加载的方法试试，结果还是一样，看来没有必要把原本的onload事件绑定也改用$(document).ready()来写。那是什么原因使得Firefox正常而IE就能呢？接着调试，发现IE下原来绑定的onload方法竟然先于$(document).ready()的内容执行，而Firefox则是先执行$(document).ready()的内容，再执行原来的onload方法。这个和网上的说法似乎不完全一致啊，呵呵，有点意思，好像越来越接近真相了。

翻翻jquery的源码看看$(document).ready()是如何实现的吧：

```javascript
f ( jQuery.browser.msie && window == top ) (function(){
    if (jQuery.isReady) return;
    try {
        document.documentElement.doScroll("left");
    } catch( error ) {
        setTimeout( arguments.callee, 0 );
        return;
    }
    // and execute any waiting functions
    jQuery.ready();
})();
jQuery.event.add( window, "load", jQuery.ready );
```

结果很明了了，IE只有在页面不是嵌入frame中的情况下才和Firefox等一样，先执行$(document).ready()的内容，再执行原来的onload方法。对于嵌入frame中的页面，也只是绑定在load事件上执行，所以自然是在原来的onload绑定的方法执行之后才轮到。而这个页面中正好在测试环境下有一个访问不到的资源，那十几秒的延迟正是它放大出的时间差。