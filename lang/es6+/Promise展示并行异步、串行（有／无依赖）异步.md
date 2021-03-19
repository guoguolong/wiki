## 先介绍下Promise如何替代回调

```js
'use strict';

let promise = new Promise(function(resolve, reject){
    resolve('hello')
});

let promise2 = new Promise(function(resolve, reject){
    resolve('yap')
});

promise.then(function(r) {
    console.error(r);
    return promise2;
}).then(function(r) {
    console.error("r2:" + r);
    return promise2;
}).then(function(r) {
    console.error("r3:" + r);
})
```

## 并行、串行

```js
'use strict';

function req1(params) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            resolve('{'+ params +'}');
        }, 30);
    });
}

function req2(params) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            resolve('<'+ params +'>');
        }, 30);
    });
}

Promise.all([req1('tiger'),req2('lion')]).then(function (result) {
    console.log('并行无依赖', result);
});

req2('shark').then(function(result1) {
    req1(result1 + '-fishes').then(function(result2) {
        let result = [result1, result2];
        console.log("串行有参数依赖，并合并结果(风格I): ", result);
    });
});

let finalResult = [];
req2('shark').then(function(result) {
    finalResult.push(result);
    // 组装data，作为req1的参数
    let param = 'fish: ' + result;
    return req1(param);
}).then(function(result) {
    finalResult.push(result);
    console.log("串行有参数依赖，并合并结果(风格II): ", finalResult);
});
```

预期使出结果：

> 并行无依赖 [ '{tiger}', '<lion>' ]
> 串行有参数依赖，并合并结果(风格I): [ '<shark>', '{<shark>-fishes}' ]
> 串行有参数依赖，并合并结果(风格II): [ '<shark>', '{fish: <shark>}' ]