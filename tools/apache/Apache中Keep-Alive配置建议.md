
在 Apache 服务器中，Keep-Alive 是一个布尔值，On 代表打开，Off 代表关闭，这个指令在其他众多的 HTTPD 服务器中都是存在的。

Keep-Alive 配置指令决定当处理完用户发起的 HTTP 请求后是否立即关闭 TCP 连接，如果 Keep-Alive 设置为On，那么用户完成一次访问后，不会立即断开连接，如果还有请求，那么会继续在这一次 TCP 连接中完成，而不用重复建立新的 TCP 连接和关闭TCP 连接，可以提高用户访问速度。

那么我们考虑3种情况：
* 用户浏览一个网页时，除了网页本身外，还引用了多个 javascript 文件，多个 css 文件，多个图片文件，并且这些文件都在同一个 HTTP 服务器上。
* 用户浏览一个网页时，除了网页本身外，还引用一个 javascript 文件，一个图片文件。
* 用户浏览的是一个动态网页，由程序即时生成内容，并且不引用其他内容。

对于上面3中情况，我认为：1 最适合打开 Keep-Alive ，2 随意，3 最适合关闭 Keep-Alive

下面我来分析一下原因。
在 Apache 中，打开和关闭 Keep-Alive 功能，服务器端会有什么异同呢？

1. 先看看理论分析。

打开 Keep-Alive 后，意味着每次用户完成全部访问后，都要保持一定时间后才关闭会关闭 TCP 连接，那么在关闭连接之前，必然会有一个Apache 进程对应于该用户而不能处理其他用户，假设 Keep-Alive 的超时时间为 10 秒种，服务器每秒处理 50个独立用户访问，那么系统中 Apache 的总进程数就是 10 * 50 ＝ 500 个，如果一个进程占用 4M 内存，那么总共会消耗 2G内存，所以可以看出，在这种配置中，相当消耗内存，但好处是系统只处理了 50次 TCP 的握手和关闭操作。

如果关闭 Keep-Alive，如果还是每秒50个用户访问，如果用户每次连续的请求数为3个，那么 Apache 的总进程数就是 50 * 3= 150 个，如果还是每个进程占用 4M 内存，那么总的内存消耗为 600M，这种配置能节省大量内存，但是，系统处理了 150 次 TCP的握手和关闭的操作，因此又会多消耗一些 CPU 资源。

2. 再看看实践的观察。
我在一组大量处理动态网页内容的服务器中，起初打开 Keep-Alive功能，经常观察到用户访问量大时Apache进程数也非常多，系统频繁使用交换内存，系统不稳定，有时负载会出现较大波动。关闭了 Keep-Alive功能后，看到明显的变化是： Apache 的进程数减少了，空闲内存增加了，用于文件系统Cache的内存也增加了，CPU的开销增加了，但是服务更稳定了，系统负载也比较稳定，很少有负载大范围 波动的情况，负载有一定程度的降低；变化不明显的是：访问量较少的时候，系统平均负载没有明显变化。

总结一下：

在内存非常充足的服务器上，不管是否关闭 Keep-Alive 功能，服务器性能不会有明显变化；
如果服务器内存较少，或者服务器有非常大量的文件系统访问时，或者主要处理动态网页服务，关闭 Keep-Alive 后可以节省很多内存，而节省出来的内存用于文件系统Cache，可以提高文件系统访问的性能，并且系统会更加稳定。

补充1：

关于是否应该关闭 Keep-Alive 选项，我觉得可以基于下面的一个公式来判断。
在理想的网络连接状况下，系统的 Apache 进程数和内存使用可以用如下公式表达：
HttpdProcessNumber = KeepAliveTimeout * TotalRequestPerSecond / Average(KeepAliveRequests)
HttpdUsedMemory = HttpdProcessNumber * MemoryPerHttpdProcess

换成中文：
总Apache进程数 = KeepAliveTimeout * 每秒种HTTP请求数 / 平均KeepAlive请求  
Apache占用内存 = 总Apache进程数 * 平均每进程占用内存数

需要特别说明的是：

[平均Keep-Alive请求] 数，是指每个用户连接上服务器后，持续发出的 HTTP 请求数。当 Keep-AliveTimeout 等 0或者 Keep-Alive 关闭时，Keep-AliveTimeout 不参与乘的运算从上面的公式看，如果 [每秒用户请求]多，[Keep-AliveTimeout] 的值大，[平均Keep-Alive请求] 的值小，都会造成 [Apache进程数] 多和 [内存]多，但是当 [平均Keep-Alive请求] 的值越大时，[Apache进程数] 和 [内存] 都是趋向于减少的。

基于上面的公式，我们就可以推算出当 平均Keep-Alive请求 <= Keep-AliveTimeout 时，关闭 Keep-Alive 选项是划算的，否则就可以考虑打开。

补充2: 

Keep-Alive 该参数控制Apache是否允许在一个连接中有多个请求，默认打开。但对于大多数论坛类型站点来说，通常设置为off以关闭该支持。 

补充3: 

如果服务器前跑有应用squid服务，或者其它七层设备,Keep-Alive On 设定要开启持续长连接
实际在前端有 squid 的情况下, Keep-Alive 很关键。记得 On