想必好多童鞋都有直接复制粘贴event.preventDefault() 或者event.stopPropagation() 的经历，但是为什么这样做不甚了解，今天我们的目的就是要彻底搞懂这一区别。

### javascript中的“事件传播”模式

为了彻底弄清楚它们之间的区别，我不得不要先说一下javascript中两种事件传播模式:

```
- 捕获模式(capturing)
- 冒泡模式(bubbling)
```

捕获模式又称为“滴流模式”(trickling)，个人认为滴流模式更好理解，滴流就是“从上向下”，而冒泡就是“从下向上”，好了，先记住这两种模式的特点。

同时你还要记住，这两种模式是为了干什么的？
这两种模式就是为了一点：决定html中“元素”(比如div, p, button)接收到事件的“顺序”！当然接收到事件的顺序不同，自然事件监听函数被触发的顺序就不同了，于是间接地就出现了监听函数被执行顺序的不同。

所以。。

#### 捕获模式

当事件发生时，该事件首先被最外层元素接受到，然后依次向内层元素传播。（从上向下）

#### 冒泡模式

当事件发生时，该事件首先被最内层元素接受到，然后依次向外层元素传播。（从下向上）

顺便提一句，以前网景公司主推捕获模式而微软则偏向于冒泡模式，不过两者都是[W3C DOM事件标准(2000)。](http://www.w3.org/TR/DOM-Level-2-Events/events.html)

IE9以下仅仅支持冒泡模式，但是IE9+以及现在的主流浏览器都支持两种模式了。

#### 声明方式

用哪种事件传播方式完全是我们自己说了算的，我们可以使用

```
addEventListener(type, listener, useCapture)
```

来注册事件处理方式，以及以何种传播模式进行。

#### 例子

```
<div id="outerMost">
    <div id="middle">
        <a href="" id="innerMost" >click</a>
    </div>
</div>
```

我们可以对上述代码添加一些样式，这样在网页中更直观。

```
<div id="outerMost" style="border: 1px solid black; width: 150px; height: 120px; padding: 20px;">
    outerMost
    <div id="middle" style="border: 1px solid black; width: 60px; height: 60px; padding: 20px;">
        Middle
        <a href="" id="innerMost" style="border: 1px solid black; width: 30px; height: 20px; display: block; margin: 20px;">click</a>
    </div>
</div>
```

如图：  
![image](http://note.youdao.com/yws/res/5729/7A4B9BF979664AE09E7F1769800D7D3D)

#### 使用事件捕获模式注册事件监听

对最外层，中间层，最内层分别用“捕获”模式注册事件监听，我们上面说了，如果使用捕获模式，那么addEventListener第三个参数应该是true，否则则是冒泡模式，如果不声明，默认为冒泡模式。

```
var outerElement = document.getElementById('outerMost');
var middleElement = document.getElementById('middle');
var innerElement = document.getElementById('innerMost');

outerElement.addEventListener('click', function () {
    console.log('trigger outermost div');
}, true);
middleElement.addEventListener('click', function () {
    console.log('trigger middle div');
}, true);
innerElement.addEventListener('click', function () {
    console.log('trigger innermost button');
}, true);
```

我们点击中间层Middle字样，如图：  

![image](http://note.youdao.com/yws/res/5731/4804E298837F4F998CC8B1DB012884A3)

可以看到，事件触发从外向里进行，如果大家把addEventListener中第三个参数改为false或者留空，点击middle字样，则会得到相反的结果，大家可以自己试一下。

### preventDefault 及 stopPropagation函数区别

终于到了谈一谈preventDefault 和 stopPropagation了，大家可能注意到了，我上面的例子中没有涉及到点击click链接，为什么呢？大家可以试一下，点击click会发生什么？它会又立即跳转到了当前页面(因为我们href是一个空连接，这是链接元素的一个默认特性)，虽然我们的监听函数被执行了，但是有时候我们不想这个默认特性被执行，比如，我们可能想在监听函数里面改变div的背景颜色等等，这样如果这个链接元素a默认特性的存在就会立即“刷新”了该页面，让我们的改变背景颜色无法进行。

所以为了“阻止”元素的“默认特性”，所以事件对象中有了一个preventDefault方法，如下：

```
innerElement.addEventListener('click', function (event) {
    event.preventDefault();
    console.log('trigger innermost button');
}, true);
```

这样我们点击click就会得到：

```
trigger outermost div
trigger middle div
trigger innermost button
```

那么stopPropagation呢？
向上面这种情况，如果当你点击click的时候，只想出发绑定在click上的监听函数，而不想触发“传播链”上的其他函数，那么则使用stopPropagation。

注意：你在那个事件监听函数中使用event.stopPropagation();那么传播链就会终止，向上面这个例子，因为我们使用的是捕获模式，即使我们添加了：

```
innerElement.addEventListener('click', function (event) {
    event.preventDefault();
    event.stopPropagation();
    console.log('trigger innermost button');
}, true);
```

依然会得到和上面一样的结果，为什么呢？因为捕获模式是由外往里传播，我们只是在a这里阻止了继续像里传播，因为没有更里的元素了，所以结果一样，为了更好地演示，我们可以把捕获模式改为冒泡模式如下：

```
var outerElement = document.getElementById('outerMost');
var middleElement = document.getElementById('middle');
var innerElement = document.getElementById('innerMost');

outerElement.addEventListener('click', function (event) {
    console.log('trigger outermost div');
});
middleElement.addEventListener('click', function () {
    console.log('trigger middle div');
});
innerElement.addEventListener('click', function (event) {
    event.preventDefault();
    event.stopPropagation();
    console.log('trigger innermost button');
});
```

这样点击click，就只得到了一条log：

```
trigger innermost button
```

### return false;

最后说一下return false; 这是jQuery中提供，比如：

```
$('#innermost').on('click', function () {
    return false;
})
```

它帮我们同时做了:

```
    - event.preventDefault();
    - event.stopPropagation();
```

这两个工作，你可以看做是一种快捷方式，但是你在原生javascript中的监听回调函数中写return false; 是没有任何用的。比如：

```
innerElement.addEventListener('click', function (event) {
    return false;
});
```

只是jQuery提供的一种特性。