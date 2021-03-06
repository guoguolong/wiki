## 背景

这是08年写的一份文档，我当时在一家网站刚接手做技术负责人，网站每天大概有60万ip/300万pv的访问，网站产品很复杂，代码结构差，开发工程师来来去去，代码只能只读了。突然有一天开始频繁出现php-fpm进程耗光内存和cpu占有率飙升，前端频繁出现504错误

**php-fpm进程耗光内存** 这个就是传说中的内存泄露，所谓内存泄露，是指进程在运行过程中，内存占用率逐步上升而不释放，导致系统可用内存越来越少的情况

严格上说，这个也不算致命错误，“内存泄露”只对长期运行的程序有威胁，对单一任务的执行脚本不需要担心

最简单的处理方式，是定时重启进程。php-fpm的配置信息里面有个max_request,就定义一个fastcgi进程处理完多少个请求之后退出这样系统可用释放掉内存，但是如果内存占用率增长速度非常快，频繁重启进程，就会影响服务的稳定性，所以这个问题必须正面解决

## 内存泄露排查非常困难

- 因为代码规模非常大，想靠做code review的方式来查基本上不可能
- php并非运行在虚拟机上，没有什么官方的monitor（类似java hprof,JVM Monitor等）
- 在互联网上搜索，找不到任何答案

## 探索解决问题

- 使用 valgrind 调试php-cgi进程 Valgrind 是一个linux常用的程序的内存调试和代码剖析，对调试C/C++程序的内存泄露很有帮助，它的机制是在系统alloc/free等函数调用上加计数。用 valgrind 调试php-cgi，要求php-cgi 是debug版本，实践证明行不通：

1. php-cgi debug 版本放在线上根本跑不起来，性能太差
2. php程序的内存泄露，是由于一些循环引用，或者gc的逻辑错误,valgrind无法探测，它适合去检查php解释器是否有内存泄露问题

- php解释器（Zend core）自带有检查内存泄露的机制 php解释器的核心代码叫做（Zend Core) 在用valgrind 调试php-cgi进程，我查看了php-cgi的代码，发现zend core 实现了内存泄露的自我检查 但是 同上原因，php-cgi debug 跑不起来，也无法得到调试信息
- FreeBSD 的 DTrace DTrace是freebsd 系统支持的核心调试器，可以在各个系统函数调用上加计数点,twitter曾经用过。这个方法最后没有使用 有如下原因：

a. 需要找一台服务器安装freebsd,并部署到线上、或者模拟负载，非常繁琐

b. 我仔细研究了DTrace的文档，发现这个可以认为是增强的 valgrind，也不能解决我们的问题

这3种方法都不行，陷入困境.但是换个角度思考:虽然解决php程序内存泄露没有方便的工具，但是 web 程序是按请求切分的，一个http请求，对应的php进程执行一个php文件

**一个自然的想法是，记录 每次 http请求处理前后php进程的内存占用率之差，然后对结果排序，就能找出，让进程内存增加可能性最大的 文件 ，这个文件导致内存泄露的可能性最大**

## 计算进程内存占用率有两种方式

- php内置函数 memory_get_usage 这个函数是 Zend Core里面一个计数器，是zend core认为的内存使用量，但是php内存泄露有可能是zend core逻辑错误导致的，所以memory_get_usage不一定可靠
- linux 系统文件 /proc/{$pid}/status 会记录某个进程的运行状态，里面的 VmRSS 字段记录了该进程使用的常驻物理内存(Residence)，这个就是该进程实际占用的物理内存了，用这个数据比较靠谱，在程序里面提取这个值也很容易 找到思路，就可以开始动手写程序
- 直接修改了php-cgi的源代码，在main.c里面处理每个fastcgi请求前后分别加计数代码，输出日志到log文件，重新编译上线 运行30分钟之后，执行 cat short.log| awk '{print $3 "\t" $7 "\t" $6 "\t" $4$5}' |sort -r -n |head -n 100 很容易找到最可能出现内存泄露的代码文件，然后进一步排查，重构代码，这就很简单了：能不加载的文件就不加载，大数组用完之后赶紧unset .... 搞定！

## 后面的故事

后来，我才发现其实不需要去修改php的源代码，php.ini配置文件里面有两个配置项： auto_append_file,auto_prepend_file，可以在请求前后注入代码 ....

真是悲剧

web程序做性能优化也是这个思路，但是要简单很多，无需写代码，在nginx log里面加上$request_time ，用awk/sort 处理一下就可以找出瓶颈。