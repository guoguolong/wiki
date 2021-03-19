1. 它是一个Javascript运行环境
2. 依赖于Chrome V8引擎进行代码解释
3. 事件驱动
4. 非阻塞I/O
5. 轻量、可伸缩，适于实时数据交互应用
6. 单进程，单线程

node.js 是单线程，异步非阻塞I/O

## npm常用全局模块

```
npm i @tuniu/light-cli -g
npm i @tuniu/pack-cli -g
npm i cnpm -g
npm i pm2 -g
npm i npm -g
npm i webpack -g
npm i egg-init -g
npm install -g @vue/cli
```

＊ NODE 第三方模块

连接池：https://segmentfault.com/a/1190000002432591

CAS资料： https://github.com/TencentWSRD/connect-cas2

工具类

npm install -g api-mock npm install -g aglio 开发包类

connect-flash插件.

内存泄漏分析： http://web.jobbole.com/85684/

＊ nodejs好文链接

http://my.oschina.net/u/1466553/blog/294336 页面字体好漂亮

- GRPC

需要用到 grpc 的同学们。

如果在执行 npm install 的时候经常需要从源代码编译 grpc 二进制文件的话， 可以在 npm 的配置里添加国内镜像源，就不用每次编译了 grpc_binary_host_mirror=http://npm.taobao.org/mirrors/grpc/

在命令行下运行 npm config set -g grpc_binary_host_mirror http://npm.taobao.org/mirrors/grpc/ 可以添加全局配置