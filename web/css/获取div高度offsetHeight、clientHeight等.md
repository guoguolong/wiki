## offsetHeight 与 clientHeight
这两个属性都能获取元素的高度，它们有什么区别呢？
代码说话~

```html
<!DOCTYPE html>
<html>
    <head>
        <title>demo</title>
        <meta charset="utf-8">
        <style type="text/css">
            #demo {
                width: 100px;
                height: 200px;
                background: yellow;
                margin: 10px;
                padding: 10px;
                border: 2px solid blue;
            }
        </style>
    </head>
    <body>
        <div id="demo">hello</div>
        <script type="text/javascript">
            var div = document.getElementById('demo');
            console.log(div.offsetHeight); // 224
            console.log(div.clientHeight); // 220
        </script>
    </body>
</html>
```

可以看出
>offsetHeight = content + border + padding = 200 + 2 * 2 + 10 * 2 = 224
>clientHeight = content + padding = 200 + 2 * 10 = 220

两个属性的差距于是就显而易见了。同样返回元素的高度，**offsetHeight的值包括元素内容+内边距+边框，而clientHeight的值等于元素内容+内边距。区别就在于有没有边框~**

## getComputedStyle
有小伙伴可能会说，我们可以直接利用`style`属性获取元素高度。然而在上面的代码中，

```
console.log(div.style.height) // ''
```

**为空！**
这是因为`style`属性只能获取元素标签`style`属性里的值。对于一个光秃秃的`<div>`，`style`的输出是空的。
下面把内部样式表中的高度注释掉，写在行内样式中，改动如下。

```css
#demo {
    width: 100px;
    /*height: 200px;*/
    background: yellow;
    margin: 10px;
    padding: 10px;
    border: 2px solid blue;
}
<div id="demo" style="height: 200px">hello</div>
```

这个时候`style`属性的输出为

```javascript
console.log(div.style.height) // 200px
```
此时获得元素高度。问题在于：**如果我们每次都要写成内联样式，也太费事了吧。那么，该怎么办？**

`getComputedStyle`方法获取的是最终应用在元素上的所有CSS属性对象（即使没有CSS代码，也会把默认的祖宗八代都显示出来）；这和`style`属性只能获取**内联样式**的行为形成了鲜明的对比。除此之外，`getComputedStyle`是只读的，但是`style`能文能武，可读可写，我们也可以利用它动态设置元素的高度。
我们只需输入这么一行代码。

```javascript
window.getComputedStyle(div);
```

可以清晰地看到，`getComputedStyle`方法取得了元素的**所有样式**。如果只想要高度，那就让**`getPropertyValue`**方法来帮忙**`getPropertyValue`方法可以获取CSS样式申明对象上的属性值**。
如：

```javascript
console.log(window.getComputedStyle(div).getPropertyValue('height')); // 200px
```

问题解决。

### 补充 1:  IE的 currentStyle 属性
顺便要说一下，`currentStyle`属性也可以利用，不过这是IE浏览器的自娱自乐。兼容性极差。
想要更深入研究的，请参考张鑫旭大神的博客[获取元素CSS值之getComputedStyle方法熟悉](http://www.zhangxinxu.com/wordpress/2012/05/getcomputedstyle-js-getpropertyvalue-currentstyle/)，再次膜拜大神~

### 补充2: Element.getBoundingClientRect()
还有一种方法：**Element.getBoundingClientRect()**方法获取与元素相关的CSS属性边框集合。返回对象中共有6个属性。

当计算边界矩形时，会考虑视口区域（或其他可滚动元素）内的滚动操作，也就是说，当滚动位置发生了改变，top和left属性值就会随之立即发生变化（因此，它们的值是相对于视口的，而不是绝对的）。如果不希望属性值随视口变化，那么只要给top、left属性值加上当前的滚动位置（通过window.scrollX和window.scrollY），这样就可以获取与当前的滚动位置无关的常量值。
详情请参考[Element.getBoundingClientRect()](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)