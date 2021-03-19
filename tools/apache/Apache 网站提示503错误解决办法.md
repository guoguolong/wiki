本文章来总结与介绍关于网站提示503错误问题分析与问题的解决办法，有碰到此类问题的朋友可参考。

Apache status 503 的原因大致有如下几种情况 ：

```
1、 CPU 负载过高，服务器响应不过来，返回503
2、 系统连接数超限，超过MaxVhostClients的上限，返回503
3、 单IP连接数超限，超过MaxConnPerIP的上限，返回503
```

进过排查发现是Apache的mod_bw模块的设置造成的

```
ForceBandWidthModule On
BandWidthModule On
BandWidth all 1024000
MaxConnection all 150
```

解决办法我们可以增大增大MaxConnection来解决，如果你服务器资源够用我们可以直接

```
MaxConnection all 0
```