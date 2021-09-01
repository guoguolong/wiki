# UnderScore源码看防抖和节流

简单说来：

- Debounce：一部电梯停在某一个楼层，当有一个人进来后，20秒后自动关门，这20秒的等待期间，又一个人按了电梯进来，这20秒又重新计算，直到电梯关门那一刻才算是响应了事件。
- Throttle：好比一台自动的饮料机，按拿铁按钮，在出饮料的过程中，不管按多少这个按钮，都不会连续出饮料，中间按钮的响应会被忽略，必须要等这一杯的容量全部出完之后，再按拿铁按钮才会出下一杯。



## 防抖 (debounce)

防抖，顾名思义，防止抖动，以免把一次事件误认为多次，敲键盘就是一个每天都会接触到的防抖操作。

想要了解一个概念，必先了解概念所应用的场景。在 JS 这个世界中，有哪些防抖的场景呢

- 登录、发短信等按钮避免用户点击太快，以致于发送了多次请求，需要防抖
- 调整浏览器窗口大小时，resize 次数过于频繁，造成计算过多，此时需要一次到位，就用到了防抖
- 文本编辑器实时保存，当无任何更改操作一秒后进行保存

代码如下，可以看出来「**防抖重在清零 clearTimeout(timer)**」

```javascript
function debounce (f, wait) {
  let timer
  return (...args) => {
    clearTimeout(timer)
    timer = setTimeout(() => {
      f(...args)
    }, wait)
  }
}
```



## 节流 (throttle)

节流，顾名思义，控制水的流量。控制事件发生的频率，如控制为1s发生一次，甚至1分钟发生一次。与服务端(server)及网关(gateway)控制的限流 (Rate Limit) 类似。

- scroll 事件，每隔一秒计算一次位置信息等

- 浏览器播放事件，每个一秒计算一次进度信息等

- input 框实时搜索并发送请求展示下拉列表，没隔一秒发送一次请求 (也可做防抖)

代码如下，可以看出来「**节流重在开关锁 timer=null**」

  ```javascript
  function throttle (f, wait) {
    let timer
    return (...args) => {
      if (timer) { return }
      timer = setTimeout(() => {
        f(...args)
        timer = null
      }, wait)
    }
  }
  ```

## underscore的实现
### _.debounce
```javascript
_.debounce = function(func, wait, immediate) {
  //timeout存储定时器的返回值  result返回func的结果
  var timeout, result;
  
  //定时器触发函数
  var later = function(context, args) {
    timeout = null;
    if (args) result = func.apply(context, args);
  };

  var debounced = restArguments(function(args) {
    if (timeout) clearTimeout(timeout);//如果存在定时器，先清除原先的定时器
    if (immediate) {
      var callNow = !timeout;
      timeout = setTimeout(later, wait);//启动一个定时器
      if (callNow) result = func.apply(this, args);//如果immediate为true，那么立即执行函数
    } else {
      timeout = _.delay(later, wait, this, args);//同样启动一个定时器
    }

    return result;
  });

  debounced.cancel = function() {
    clearTimeout(timeout);
    timeout = null;
  };

  return debounced;
};
```
### _.throttle

```javascript
 _.throttle = function(func, wait, options) {
    //timeout存储定时器  context存储上下文 args存储func的参数  result存储func执行的结果
    var timeout, context, args, result;
    var previous = 0;//记录上一次执行func的时间，默认0，也就是第一次func一定执行（now-0）大于wait
    if (!options) options = {};//默认options
 
    //定时器函数
    var later = function() {
      //记录这次函数执行时间
      previous = options.leading === false ? 0 : _.now();
      timeout = null;
      result = func.apply(context, args);//执行函数func
      if (!timeout) context = args = null;
    };
 
    var throttled = function() {
      var now = _.now();//当前时间
      //如果第一次不执行，previous等于当前时间
      if (!previous && options.leading === false) previous = now;
      //时间间隔-(当前时间-上一次执行时间)  
      var remaining = wait - (now - previous);
      context = this;
      args = arguments;
      //如果remaining<0,那么距离上次执行时间超过wait,如果(now-previous)<0,也就是now<previous
      if (remaining <= 0 || remaining > wait) {
        //清除定时器
        if (timeout) {
          clearTimeout(timeout);
          timeout = null;
        }
        previous = now;//记录当前执行时间
        result = func.apply(context, args);//执行函数func
        if (!timeout) context = args = null;
      } else if (!timeout && options.trailing !== false) {
        //如果不禁用最后一次执行(trailing为true)，定时执行func
        timeout = setTimeout(later, remaining);
      }
      return result;
    };
 
    throttled.cancel = function() {
      clearTimeout(timeout);
      previous = 0;
      timeout = context = args = null;
    };
 
    return throttled;
  }
```
这里是一个代码例子： https://github.com/guoguolong/codes.gist/blob/master/web/js/debounce-throttle.html

使用了剥离的 underscore的 throttle 和简化的 debounce.

## 总结

节流和防抖是JavaScript中一个非常重要的知识点，我们首先要知道：

1. 节流是“第一个说了算”，后续都会被节流阀屏蔽掉，
2. 防抖是“最后一个说了算”，邪恶的魔鬼每个多会启动一个定时炸弹，只有后面的定时炸弹到了才会拆掉前面的炸弹，但是最后还是会延迟起爆。

根据这个思想，我们利用闭包的思想能够手写实现它们。根据underscore的源码我们能够更好更灵活的利用它们。
