## 什么是继承？

继承是面向对象最显著的一个特性。继承是从已有的类中派生出新的类，新的类能吸收已有类的数据属性和行为，并能扩展新的能力。

在JavaScript 中 没有 类的概念， 它是通过构造函数来产生 对象，

构造函数 就是一个普通的函数，通常当函数名 为 大写开头的，我们认为是构造函数，否则 就是普通的方法。

```javascript
    function A() {  
        this.name = 'A Class instance';  
    }        
    function m1() {        
    }  
```

既然 Javascript 是 通过构造函数来产生 对象，那我们怎么定义它的 属性、方法呢？

var a1 = new A(); 是新创建一个A类对象，默认情况下，在构造函数中，使用this指向的是 新创建的对象；

而Javascript的对象属性 可以 晚绑定，即

```javascript
var obj = {};

obj.name = 'obj1';

obj.say = function say() {

    console.log(this.name);

}
```

我们分析一下上面代码，

创建了 a1，a2 对象，在创建a1，a2 对象的时候 都为其添加一个say()方法,而这2个对象的 say方法 完全一样，

试想想 如果 创建n 个 A类对象，那是不是为这n个对象 添加一个say()方法，那是非常浪费内存。

所以在Javascript 中 引用了 prototype 原型的概念：

每一个构造函数都有一个prototype 对象，使用构造函数实例化一个对象，访问这个对象属性时，如果这个对象有该属性，则返回，否则就会在该对象的构造函数的prototype 上找，直到找到就返回，否则返回的是undefined

```
Object.prototype.say = function() {
    console.log('Hi, I am' + this.name);
}
function A() {
    this.name = 'A Class Instance';
}
var a1 = new A();
var a2 = new A();
a1.say(); // Hi I am A Class Instance
a2.say(); // Hi I am A Class Instance
```

在上面代码中，A构造函数中没有定义say 方法，但a1，a2 却 能够 调用say()方法，因为 A函数的prototype 默认指向的是 Object.prototype ；此时内存中只保存Object.prototype.say,而A产生的对象 是通过原型机制，一层一层往上找，然后调用的。所以通过 prototype原型机制，我们可以实现代码复用，和继承

```javascript
    A.prototype.say = function() {  
        console.log('Hi,I am ' + this.name);  
    }  
    function A() {  
        this.name = 'A Class instance';  
    }  
    var a1 = new A();  
    var a2 = new A();  
    a1.say(); // Hi,I am A Class instance  
    a2.say(); // Hi,I am A Class instance        
    function B() {  
        this.name = 'B Class instance';  
    }  
    B.prototype = new A();  
    B.prototype.constructor = B;        
    var b = new B();  
    b.say();  
```

分析一下上面代码 执行结果：

我们定义了一个B类，并把他的prototype 指向 A的实例对象，然后产生一个 b 对象，调用b对象 say() ,也输出了内容。

这是为什么呢？ 访问 b 对象属性时， 如果不存在 ，就会在其 prototype 访问，就是 访问a 对象的 prototype ，但a对象 prototype 默认 是 指向Object.prototype 的，而我们在 Object.prototype 定义了say 方法，b 对象也能访问 say()方法， 就好像b 继承了 父类中的 属性一样。

这里我们也可以看出，一旦 原型链 过长，会导致 访问多个对象的prototype。所以 在设计 的时候 应该 不超过 3层原型链，可以考虑其他方式。

Javascript 最佳的实践：


```javascript
// 定义动物类
function Animal(type, name) {
    // 定义属性
    this.type = type;
    this.name = name;
}
// 定义方法
Animal.prototype.say = function() {
    console.log('Hi,My name is %s ,I am a %s', this.name, this.type);
}
// 定义Dog 类
function Dog(type, name, hobby) {
    // 这里的this 指向的是新创建的dog对象,而通过 Animal.apply 形式调用 Animal方法 ,
    // 显示的指定其 this ,指向新创建的dog对象 ,再把对象的参数传进去
    // 所以在 Animal 构造函数中 的 代码 是 为 新创建的dog对象 的属性 赋值
    // 从而实现了 属性的继承

    Animal.apply(this, [ type, name ]);
    this.hobby = hobby;
}

Dog.prototype = new Animal();
Dog.prototype.constructor = Dog;
var d = new Dog('Dog', '大黄狗', '吃骨头');
d.say();
```

定义： 通过构造函数，定义 对象的 属性。 通过原型对象，定义 对象的 方法。

继承： 使用构造函数的 prototype，实现方法的继承 在构造函数使用 父类构造函数.call(this,); 父类构造函数.apply(this,[]) 从而实现属性的继承。

ECMAScript 5中引入了一个新方法: Object.create. 可以调用这个方法来创建一个新对象. 新对象的原型就是调用create方法时传入的第一个参数:

```javascript
var obj = {
    'name':'obj1',
    say: function() {
         console.log('Hi I am ', this.name);
    }
};
var o = Object.create(obj);
o.date = new Date();
console.log(o);
o.say();
```

谨记：Javascript 并不是继承了原型对象中的属性，而是在访问对象的属性时，依次在原型对象上访问，从而达到继承的效果，这就是Javascript继承的本质，所以我们只需建立 对象（子对象）的原型 与 对象（父对象）的关系即可。 不要在Object.prototype 定义任何内容。继承还可以有很多种 实现方式， 比如 属性的复制等等，应当灵活运用。