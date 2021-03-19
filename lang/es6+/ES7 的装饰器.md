## 装饰器用途

- 装饰器在javascript中仅仅可以修饰类和属性，不能修饰函数。
- 装饰器对类的行为的改变，是代表编译时发生的，而不是在运行时。
- 装饰器能在编译阶段运行代码。
- 装饰器是经典的AOP模式的一种实现方式。

## 快速上手

### 安装 babel 环境

注意安装 ： `@babel/plugin-proposal-decorators`

- .babelrc

```json
{
  "presets": [ "@babel/preset-env" ],
  "plugins": [
    ["@babel/plugin-proposal-decorators", { "legacy": true }]
  ]
}
```

### 做实验

#### 1. 修饰类（增强类功能，给类添加新方法、新属性）

- sample1.js

```js
function connnect(initSteps) {
    return (target, property, descriptor) => {
        target.prototype.address = 'McDonalds Nanjing';
        target.prototype.walk = (steps) => {
            console.log('Walked steps ' +  (initSteps + steps));
        }
    }
}

@connnect(100)
class Sample {
}

const sample =  new Sample();
console.log('Addr: ' + sample.address)
sample.walk(5);
```

执行 `babel-node sample1.js`

#### 2. 修饰方法（改变方法行为）

- sample2.js

```js
function increase(initVal) {
    return (target, property, descriptor) => {
        let func = descriptor.value // 获取目标的init方法
        descriptor.value = function() {
            arguments[0] *= initVal;
            return func.apply(target, arguments);
        }

    }
}
class MathSample {
    @increase(2)
    @increase(5)
    update(count) { 
        this.count += count;
    }

    print() {
        console.log('count:' , this.count)
    }
}
MathSample.prototype.count = 2000;

// Main Entry
const mathSample =  new MathSample();
mathSample.update(10);
mathSample.print();  // 结果: 2100
```

执行 `babel-node sample2.js`