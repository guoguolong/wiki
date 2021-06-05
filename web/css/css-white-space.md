# CSS white-space

white-space 这个东西太有用了

## 浏览器支持

IE	Firefox	Chrome	Safari	Opera

所有浏览器都支持 white-space 属性。

注释：任何的版本的 Internet Explorer （包括 IE8）都不支持属性值 "inherit"。

## 定义和用法
white-space 属性设置如何处理元素内的空白。

这个属性声明建立布局过程中如何处理元素中的空白符。值 pre-wrap 和 pre-line 是 CSS 2.1 中新增的。

```
默认值：	normal
继承性：	yes
版本：	CSS1
JavaScript 语法：	object.style.whiteSpace="pre"
```
## 可能的值

```
值	描述
normal	默认。空白会被浏览器忽略。
pre	空白会被浏览器保留。其行为方式类似 HTML 中的 <pre> 标签。
nowrap	文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。
pre-wrap	保留空白符序列，但是正常地进行换行。
pre-line	合并空白符序列，但是保留换行符。
inherit	规定应该从父元素继承 white-space 属性的值。
```