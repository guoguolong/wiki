## DOM事件级别

| LEVEL | SUPPORTS                                             |
| ----- | ---------------------------------------------------- |
| DOM 0 | element.onclick = function(){}                       |
| DOM 2 | element.addEventListener(‘click’,function(){},false} |
| DOM 3 | element.addEventListener(‘keyup’,function(){},false} |

因为DOM 1一般只有设计规范没有具体实现,所以一般跳过. 其中 IE 浏览器的 addEventListener函数对就是 attachEvent函数

## EVENT对象

| 函数                     | 功能                 |
| ------------------------ | -------------------- |
| preventDefault           | 阻止默认行为         |
| stopPropagation          | 停止事件传递         |
| stopImmediatePropagation | 阻止其它绑定事件执行 |
| currentTarget            | 当前发生事件的元素   |
| target                   | 触发事件的节点       |