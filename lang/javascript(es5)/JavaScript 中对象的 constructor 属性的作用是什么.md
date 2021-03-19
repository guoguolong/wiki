```javascript
var a,b; 

(function() { 

    function A (arg1,arg2) { 
        this.a = 1; 
        this.b = 2; 
    }
    A.prototype.log = function () { 
        console.log(this.a); 
    }
    a = new A(); 
    b = new A(); 
})();
a.log(); // 1 
b.log(); // 1
```

通过以上代码我们可以得到两个对象，a,b，他们同为类A的实例。因为A在闭包里，所以现在我们是不能直接访问A的，那如果我想给类A增加新方法怎么办？

```javascript
// a.constructor.prototype 在chrome,firefox中可以通过 a.__proto__ 直接访问
a.constructor.prototype.log2 = function () {
  console.log(this.b)
}

a.log2();
// 2
b.log2();
// 2
```

通过访问constructor就可以了。 或者我想知道a的构造函数有几个参数？

```javascript
a.constructor.length
```

或者再复杂点，我想知道a的构造函数的参数名是什么(angular的依赖注入就是通过此方法实现的据说)

```javascript
a.constructor
 .toString()
 .match(/\(.*\)/)
 .pop().slice(1,-1)
 .split(',');
// ["arg1", "arg2"]
```