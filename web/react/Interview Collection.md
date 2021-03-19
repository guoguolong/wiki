### √ NodeJS MicroTask
### √ 为什么 ES6 为什么可以做静态分析
es6 为什么可以做静态分析，因为 es6 的模块系统是有严格定义的，import 只能在文件顶部，import 没法放在 if 里（ conditional import ）, 所以是可以做静态分析的
### √ requestAnimationFrame
https://www.jianshu.com/p/f6d933670617

### √ MutationObserver
https://zcfy.cc/article/three-real-world-uses-for-mutation-observer
### js

* const 定义数组或者JSON ,能不能修改， 能修改的话，为什么
* async函数中 有多个wait， 他们同步还是异步执行？ 为什么？
* √ promise.all 怎么实现的
* js 防抖 节流: https://www.cnblogs.com/momo798/p/9177767.html
* 还有就是面试的 es6 7

### AMD CMD commonJS

CMD是延迟执行的，而AMD是提前执行的。
RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。

### react
* build编译的时候 干了啥 。
* react-route实现原理 
* redux实现原理

### webpack
* webpack loader 的，他问我为什么执行顺序是从后往前的
* loader 执行顺序为什么是从后往前，我觉得我会回答，loader 
* loader 和 plugin 的区别
* 如何利用webpack来优化前端性能
* 单页和多页的配置
* js 工程化怎么做，要实现哪些？
*  webpack打包编译原理

### 怎么编译es6 ,es7, less sass
### NODE
* 负载均衡
* 怎么监控处理内存泄漏
* SSO
* SSR

* NodeJS其实就是js异步的执行顺序   I/O、setTimeout、setInterval、setImmediate、requestAnimationFrame
* 为什么 ES6 为什么可以做静态分析
1. es6 为什么可以做静态分析，因为 es6 的模块系统是有严格定义的，import 只能在文件顶部，import 没法放在 if 里（ conditional import ）, 所以是可以做静态分析的
* webpack loader 的，他问我为什么执行顺序是从后往前的
2. loader 执行顺序为什么是从后往前，我觉得我会回答，loader 是如何组合的，本质是使用了 reduce，如果使用的是 reduceRight 就是从前往后，再扩展点的话可以说 redux 的中间件也是从后往前的组合的。

* 微任务：process.nextTick、MutationObserver、Promise.then catch finally
* js
    * const 定义数组或者JSON ,能不能修改， 能修改的话，为什么
    * promise.all 怎么实现的
    * js 防抖 节流: https://www.cnblogs.com/momo798/p/9177767.html
    * 还有就是面试的 es6 7 ， 面react的很少
    * async函数中 有多个wait ， 他们同步还是异步执行？ 为什么？
* 还有AMD CMD commonJS   规范
* react
    * build编译的时候 干了啥 。
* webpack
    * loader 和 plugin 的区别
    * 如何利用webpack来优化前端性能
    * 单页和多页的配置
    * js 工程化怎么做，要实现哪些？
* 怎么编译es6 ,es7   less sass
* NODE
    * 负载均衡
    * 怎么监控处理内存泄漏
    * SSO
    * SSR
*  react-route实现原理 
*  webpack打包编译原理
*  redux实现原理