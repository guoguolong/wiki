# 使用 Preload/Prefetch 优化页面
[toc]

## preload 提前加载

preload 顾名思义就是一种预加载的方式，它通过声明向浏览器声明一个需要提前加载的资源，当资源真正被使用的时候立即执行，就无需等待网络的消耗。

它可以通过 Link 标签进行创建：

```html
<!-- 使用 link 标签静态标记需要预加载的资源 -->
<link rel="preload" href="/path/to/style.css" as="style">

<!-- 或使用脚本动态创建一个 link 标签后插入到 head 头部 -->
<script>
const link = document.createElement('link');
link.rel = 'preload';
link.as = 'style';
link.href = '/path/to/style.css';
document.head.appendChild(link);
</script>
```

当浏览器解析到这行代码就会去加载 href 中对应的资源但不执行，待到真正使用到的时候再执行

使用 as 来指定将要预加载的内容的类型，将使得浏览器能够：

- 更精确地优化资源加载优先级。
- 匹配未来的加载需求，在适当的情况下，重复利用同一资源。
- 为资源应用正确的内容安全策略。
- 为资源设置正确的 Accept 请求头

### 使用 HTTP 响应头的 Link 字段创建

```jsx
Link: <https://example.com/other/styles.css>; rel=preload; as=style
```

使用 preload 前，在遇到资源依赖时才会进行加载；使用之后浏览器会进行资源调度，将 link 指定的资源优先加载，不管资源是否使用

### 使用 preload 需要注意的点

1. 不要滥用 preload 如果不确定资源是否使用，则不要无意义的使用 preload，尤其是在移动端，会浪费用户带宽

2. preload 会使资源优先加载，但不一定会提升优先级

3. 不要混淆 preload 和 prefetch
   - prefetch 是一种期望，预测会加载指定的资源，以备下一个导航或者下一屏页面使用，但对当前的页面并没有什么帮助。如果 prefetch 使用不得当，还会造成资源重复加载的问题。页面不一定会使用 prefetch 指定的资源。

   - preload 是一种肯定，确认会加载指定资源，在页面加载的生命周期的早期阶段就开始获取，不区分下一屏。页面一定会使用 preload 指定的资源（不使用将会报警告 ⚠️）

4. 使用 preload 预加载跨域资源时，需要设置 crossorigin 属性。


如果你需要获取的是 font 文件，那么即使是非跨域的情况下，也需要设置 crossorigin。

```link
<link rel="preload" href="https://mdn.github.io/html-examples/link-rel-preload/fonts/fonts/cicle_fina-webfont.woff2 " as="font" crossorigin="anonymous">
```

## prefetch 预判加载

prefetch 跟 preload 不同，它的作用是告诉浏览器未来可能会使用到的某个资源，浏览器就会在闲时去加载对应的资源，若能预测到用户的行为，比如懒加载，点击到其它页面等则相当于提前预加载了需要的资源。它的用法跟 preload 是一样的

```html
<!-- link 模式 -->
<link rel="prefetch" href="/path/to/style.css" as="style">

<!-- HTTP 响应头模式 -->
Link: <https://example.com/other/styles.css>; rel=prefetch; as=style
```

### 细节

1. 当一个资源被 preload 或者 prefetch 获取后，它将被放在内存缓存中等待被使用，如果资源存在有效的缓存标志（如 cache-control 或 max-age），它将被存储在 HTTP 缓存中可以被不同页面所使用。
2. 正确使用 preload/prefetch 不会造成二次下载，也就说：当页面上使用到这个资源时候 preload 资源还没下载完，这时候不会造成二次下载，会等待第一次下载并执行脚本。
3. 对于 preload 来说，一旦页面关闭了，它就会立即停止 preload 获取资源，而对于 prefetch 资源，即使页面关闭，prefetch 发起的请求仍会进行不会中断。

**preload 是告诉浏览器页面必定需要的资源，浏览器一定会加载这些资源，而 prefetch 是告诉浏览器页面可能需要的资源，浏览器不一定会加载这些资源。**

没有用到的 preload 资源在 Chrome 的 console 里会在 onload 事件 3s 后发生警告。