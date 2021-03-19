# Shim

一个shim就是一个库，它将一个新的API引入到一个旧的环境中，而且仅靠旧环境中已有的手段实现。 比如ES5-shim, github地址：http://github.com/es-shims/es5-shim/，它在ECMAScript 3的引擎上实现了ECMAScript 5的新特性,而且在Node.js上和在浏览器上有完全相同的表现(因为它能在Node.js上使用,不光浏览器上,所以它不是polyfill).

# Polyfill

在2010年10月份的时候,Remy Sharp在博客上发表了一篇关于术语"polyfill"的文章,

一个polyfill是一段代码(或者插件),提供了那些开发者们希望浏览器原生提供支持的功能. 因此,一个polyfill就是一个用在浏览器API上的shim.我们通常的做法是先检查当前浏览器是否支持某个API,如果不支持的话就加载对应的polyfill.然后新旧浏览器就都可以使用这个API了.

术语polyfill来自于一个家装产品Polyfilla:

Polyfilla是一个英国产品,在美国称之为Spackling Paste(译者注:刮墙的,在中国称为腻子).记住这一点就行:把旧的浏览器想象成为一面有了裂缝的墙.这些[polyfills]会帮助我们把这面墙的裂缝抹平,还我们一个更好的光滑的墙壁(浏览器)

# Shiv

它的作用是使得不支持HTML5标签的浏览器诸如ie6-8, 支持html5标签。这也是我在看html语义化规范的时候看到的，觉得很有必要做一下。目前使用IE8的用户还是占据一部分比例的，所以为了兼容ie8，同时能使用像header、section、nav、footer这些语义化标签，我们可以采用shiv库来实现。

比如著名的HTML5兼容库html5shiv，Github地址：https://github.com/aFarkas/html5shiv

**polyfill 是 shim 的一种。shim 是将不同 api 封装成一种，比如 jQuery 的 $.ajax 封装了 XMLHttpRequest 和 IE 用 ActiveXObject 方式创建 xhr 对象；**



**polyfill 特指 shim 成的 api 是遵循标准的，其典型做法是在IE浏览器中增加window.XMLHttpRequest ，内部实现使用 ActiveXObject。**