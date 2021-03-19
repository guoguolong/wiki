```html
<style>
#a{
	height:200px;
	width:200px;
	background-color:red;
    border:1px solid gray;
    box-shadow: 0px 10px 10px 0px blue;
    z-index: 1;
    position: relative;
}
#b{
	height:200px;
    width:200px;
	background-color:green;
	left:0px;
	top:0px;
}
</style>

<div id = "a">
</div>
<div id = "b">
</div>
```

shadow 不占用文档流，所以块 a 要阴影波及到 b，必须首先设置 index 大于0，另外，必须设置 position为非 static，如可以设置 position:relative