这时，CSS勇敢的站出来了，它约定了5种通用字体："serif"、"sans-serif"、"cursive"、"fantasy"、"monospace"，请注意，这5个不是5个字体，表示5类字体，比如说serif表示那种字体成比例，且上下有小横线的（参考time new roman）,那么只要符合这个特征的字体都可以算成是serif, 具体采用哪个字体，由浏览器自己根据用户电脑上安装了哪种字体采用一个默认的，各浏览器可能有所不同。几乎所有你知道的普通字体都落入这5种字体类中，这样CSS可以基本上保证一个网页呈现在不同用户的电脑上的用户体验是差不多的。

```
<meta http-equiv="content-type" content="text/html;charset=utf-8">
<style type="text/css">
.title {
 font-size:54px;
 font-family:PingFang SC,sans-serif;
 font-weight:100;
}
</style>
<span class="title">Beautiful Racoon 美丽的浣熊</span>
```

张鑫旭的博客 http://www.zhangxinxu.com/study/201703/font-family-chinese-english.html 罗列了字体的标准名（兼容性更好）