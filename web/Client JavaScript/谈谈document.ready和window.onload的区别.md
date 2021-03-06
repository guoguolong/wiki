在Jquery里面，我们可以看到两种写法:$(function(){}) 和$(document).ready(function(){})

这两个方法的效果都是一样的，都是在dom文档树加载完之后执行一个函数（注意，这里面的文档树加载完不代表全部文件加载完）。

而window.onload是在dom文档树加载完和所有文件加载完之后执行一个函数。也就是说$(document).ready要比window.onload先执行。

那么Jquery里面$(document).ready函数的内部是怎么实现的呢？下面我们就来看看：

我们来为document添加一个ready函数：

```javascript
document.ready = function (callback) {
    ///兼容FF,Google
    if (document.addEventListener) {
        document.addEventListener('DOMContentLoaded', function () {
            document.removeEventListener('DOMContentLoaded', arguments.callee, false);
            callback();
        }, false)
    }
     //兼容IE
    else if (document.attachEvent) {
        document.attachEvent('onreadytstatechange', function () {
              if (document.readyState == "complete") {
                        document.detachEvent("onreadystatechange", arguments.callee);
                        callback();
               }
        })
    }
    else if (document.lastChild == document.body) {
        callback();
    }
}
```

document.ready这个函数是实现了。我们再来验证一下最上面所说的“ready要比onload先执行”

```javascript
window.onload = function () {
    alert('onload');

};

$(document).ready(function () {
    alert('ready');

});
```

执行这段代码之后，你会看到浏览器里面会先弹出“ready”,在弹出onload。