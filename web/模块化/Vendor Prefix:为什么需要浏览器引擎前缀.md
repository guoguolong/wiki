## 浏览器引擎前缀(Vendor Prefix)是什么？

Vendor prefix—浏览器引擎前缀，是一些放在CSS属性前的小字符串，用来确保这种属性只在特定的浏览器渲染引擎下才能识别和生效。谷歌浏览器和Safari浏览器使用的是WebKit渲染引擎，火狐浏览器使用的是Gecko引擎，Internet Explorer使用的是Trident引擎，Opera以前使用Presto引擎，后改为WebKit引擎。一种浏览器引擎里一般不实现其它引擎前缀标识的CSS属性，但由于以WebKit为引擎的移动浏览器相当流行，火狐等浏览器在其移动版里也实现了部分WebKit引擎前缀的CSS属性。

## 浏览器引擎前缀(Vendor Prefix)有哪些？

```css
-moz-     /* 火狐等使用Mozilla浏览器引擎的浏览器 */
-webkit-  /* Safari, 谷歌浏览器等使用Webkit引擎的浏览器 */
-o-       /* Opera浏览器(早期) */
-ms-      /* Internet Explorer (不一定) */ 
```

## 为什么需要浏览器引擎前缀(Vendor Prefix)？

这些浏览器引擎前缀(Vendor Prefix)主要是各种浏览器用来试验或测试新出现的CSS3属性特征。可以总结为以下3点：

- 试验一些还未成为标准的的CSS属性——也许永远不会成为标准
- 对新出现的标准的CSS3属性特征做实验性的实现
- 对CSS3中一些新属性做等效语义的个性实现

这些前缀并非所有都是需要的，但通常你加上这些前缀不会有任何害处——只要记住一条，把不带前缀的版本放到最后一行：

```css
-moz-border-radius: 10px; 
-webkit-border-radius: 10px; 
-o-border-radius: 10px; 
border-radius: 10px; 
```

有些新的CSS3属性已经试验了很久，一些浏览器已经对这些属性不再使用前缀。`Border-radius`属性就是一个非常典型的例子。最新版的浏览器都支持不带前缀的`Border-radius`属性写法。

## 需要使用Vendor Prefixes的CSS3属性

主要的需要添加浏览器引擎前缀(vendor-prefix)的属性包括：

- @keyframes
- 移动和变换属性(transition-property, transition-duration, transition-timing-function, transition-delay)
- 动画属性 (animation-name, animation-duration, animation-timing-function, animation-delay)
- border-radius
- box-shadow
- backface-visibility
- column属性
- flex属性
- perspective属性

完整的列表不只这些，而且还会增加。

## 浏览器引擎前缀(vendor-prefix)的用法

当需要使用浏览器引擎前缀(vendor-prefix)时，最好是把带有各种前缀的写法放在前面，然后把不带前缀的标准的写法放到最后。比如：

```css
/* 简单属性 */
.myClass {
	-webkit-animation-name: fadeIn;
	-moz-animation-name: fadeIn;
	-o-animation-name: fadeIn;
	-ms-animation-name: fadeIn;
	animation-name: fadeIn;  /* 不带前缀的放到最后 */
}
/* 复杂属性 keyframes */
@-webkit-keyframes fadeIn {
	0% { opacity: 0; } 100% { opacity: 0; }
}
@-moz-keyframes fadeIn {
	0% { opacity: 0; } 100% { opacity: 0; }
}
@-o-keyframes fadeIn {
	0% { opacity: 0; } 100% { opacity: 0; }
}
@-ms-keyframes fadeIn {
	0% { opacity: 0; } 100% { opacity: 0; }
}
/* 不带前缀的放到最后 */
@keyframes fadeIn {
	0% { opacity: 0; } 100% { opacity: 0; }
}
```

### Internet Explorer

Internet Explorer 9 开始支持很多(但并不是全部)CSS3里的新属性。比如，你也可以在IE里使用不带浏览器引擎前缀(vendor-prefix)的**border-radius**属性。

IE6到IE8都不支持CSS3，很遗憾的是，使用这些低版本浏览器的用户还很多。所以，确保你的网站设计在不支持CSS3的情况下也能正常显示。对于一些属性：`border-radius` , `linear-gradient`, 和 `box-shadow`, 你可以使用[CSS3Pie](http://css3pie.com/)，它是一个很小的文件，把它放到你的网站的根目录下，就能让你的页面中IE6，IE8中也支持这些属性。