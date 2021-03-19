## 原生XHR

对于原生XHR对象来说，取消的ajax的关键是调用XHR对象的.abort()方法

```
var native = new XMLHttpRequest();
native.open("GET","https://api.github.com/");
native.send();
native.onreadystatechange=function(){
    if(native.readyState==4&&native.status==200){
        console.log(native.response);           
    }else{
        console.log(native.status);
    }
}
native.abort();
```

### jQuery