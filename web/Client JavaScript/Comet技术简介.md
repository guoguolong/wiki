Alex Russell（Dojo Toolkit 的项目 Lead）称这种基于 HTTP 长连接、无须在浏览器端安装插件的“服务器推”技术为“Comet”。Comet的实现方式：

* 非IE浏览器
Ajax，利用Streaming AJAX，即：readystate=3（数据仍在传输中），客户端可以读取数据，从而无须关闭连接。

* IE浏览器
仍然采用流技术，一个称为“htmlfile”的 ActiveX（结合iframe）解决了在 IE 中的加载显示问题。

所有其他技术描述都是展开参考。

参考：https://www.ibm.com/developerworks/cn/web/wa-lo-comet/  
Pushlets：http://www.pushlets.com/

## htmlfile 的 ActiveX + iframe例子

htmlfile是什么呢？这是一个类似Javascript中Window对象的一个ActiveXObject，它内部也是DOM结构，将作为隐藏帧的IFrame写入这个对象中，就可以解决进度条的问题。说的可能比较晦涩，来看实例代码吧：

### Default.aspx.cs

```
public partial class _Default : System.Web.UI.Page {    
    protected override void Render(HtmlTextWriter output) {
        string str;
        while (true) { // 死循环保持长链接     
            str = "<script >window.parent.Change('" + DateTime.Now.ToLongTimeString() + "')</script>";    
            this.Context.Response.Write(str);    
            this.Context.Response.Flush();//输脚本调用出     
            System.Threading.Thread.Sleep(1000);    
        }    
    }    
}
```
### WebForm1.aspx

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title>Asp.net Server Push</title>
    <script type="text/javascript"> 
        function Change(str){ 
            window.document.getElementById("div1").innerText=str; 
        } 
        function onload(){ 
            var ifrpush = new ActiveXObject("htmlfile"); // 创建对象 
            ifrpush.open(); //打开
            var ifrDiv = ifrpush.createElement("div"); //添加一个DIV
            ifrpush.appendChild(ifrDiv); //添加到 htmlfile
            ifrpush.parentWindow.Change=Change; //注册 javascript 方法 搞不明白为什么还要注册
            ifrDiv.innerHTML = "<iframe src='Default.aspx'></iframe>"; //在div里添加 iframe
            ifrpush.close(); //关闭
        }
        onload(); 
    </script>
</head>
<body> 
    <div style=" float:left">现在时间是：</div> 
    <div id="div1"></div> 
</body>
</html>
```