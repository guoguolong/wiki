onclick()这种写法是DOM0级规范的写法，是所有的浏览器支持的，但是这种写法有不能同时绑定多个事件、使代码耦合在了一起的弊端。但是addEventListener() 是DOM2标准中定义的方法，它可以控制是在事件捕获阶段或者是在冒泡阶段调用事件处理程序。既然这个是DOM2标准中定义的，那么只有支持DOM2级事件处理程序的浏览器才支持这个方法（IE9,Firefox,Safari,Chrome和Opera支持）。

## onclick()方式

```js
window.onload = function() {
    var container = document.querySelector("#container");
    container.onclick = function() {
        console.log("第一次onclick事件");
    }
    container.onclick = function() {
        console.log("第二次onclick事件");
    }
}　　　　　
//运行结果：“第二次onclick事件”
```

运行结果是第二个onclick把第一个onclick给覆盖了，虽然大部分情况用on就可以完成想要的结果，但是有时又需要执行多个相同的事件，很明显如果用on不能完成

## addEventListener()方法

```js
window.onload = function() {
    var container = document.querySelector("#container");
    container.addEventListener("click", function() {
        console.log("第一个addEventListener事件");
    })
    container.addEventListener("click", function() {
        console.log("第二个addEventListener事件");
    })
}
//运行结果：
//"第一个addEventListener事件"
//"第二个addEventListener事件"
```

addEventListenert方法第一个参数填写事件名，第二个参数是一个函数，第三个参数是指在冒泡阶段还是捕获阶段处理事件处理程序。true代表捕获阶段处理， false代表冒泡阶段处理。第三个参数可以省略，大多数情况也不需要用到第三个参数，不写第三个参数默认false

## 附：addEventListenert第三个参数的使用

```html
<html>
<head>
    <meta charset="UTF-8">
    <title></title>
    <style>
        #parent {
            width: 140px;
            height: 70px;
            border: 1px solid #c3c3c3;
            display: -webkit-flex;
            display: flex;
            -webkit-flex-wrap: wrap;
            flex-wrap: wrap;
            -webkit-align-content: center;
            align-content: center;
        }
    
        #child {
            width: 70px;
            height: 70px;
            margin: 0 auto;
            background-color: coral;
        }
    </style>
</head>

<body>
    <div id="parent">
        <div id="child"></div>
    </div>
    <script>
        /*parent.addEventListener("click", function() {
                        console.log("parent");
                    })

                    child.addEventListener("click", function() {
                        console.log("child");
                    })
                    //执行的结果：
                    //child
                    //parent*/

        parent.addEventListener("click", function() {
            console.log("parent");
        }, true)

        child.addEventListener("click", function() {
            console.log("child");
        })
        //执行的结果：
        //parent
        //child
    </script>
</body>
</html>
```

点击child元素，执行的顺序：child->parent。addEventListener的第三个参数写的是true，则按照事件捕获的执行顺序进行的，执行的顺序：parent->child。

## 总结

1.于是得出结果，onclick只出现一次，但是addEventListener却可以先后运行不会被覆盖，addEventListener允许给一个事件注册多个监听器。在使用DHTML库或者 Mozilla extensions 这样需要保证能够和其他的库或者差距并存的时候非常有用。

2.事件冒泡执行过程：从最具体的的元素开始向上开始冒泡

3.事件捕获执行过程：从最不具体的元素（最外面的那个盒子）开始向里面冒泡

**参考资料：**

[MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)
[MDN Web 中文文档](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)