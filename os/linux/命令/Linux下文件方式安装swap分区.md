为什么要添加Swap分区？swap分区，即交换区，作用为：当系统的物理内存不够用的时候，就需要将物理内存中的一部分空间释放出来，以供当前运 行的程序使用。那些被释放的空间可能来自一些很长时间没有什么操作的程序，这些被释放的空间被临时保存到Swap空间中，等到那些程序要运行时，再从 Swap中恢复保存的数据到内存中。这样，系统总是在物理内存不够时，才进行Swap交换。

其实，Swap的调整对Linux服务器，特别是Web服务器的性能至关重要。通过调整Swap，有时可以越过系统性能瓶颈，节省系统升级费用。

添加swap分区好处显而易见，添加方法如下，这里以阿里云服务器为例：

如果你的硬盘空间已经全部分配给其他分区,也没有多余的预算新添购硬盘,我们可以利用swap文件的方式增加虚拟的swap空间,不过执行性能会较实际的swap分区稍差。

首先，以root身份连接到服务器

选择一个目录，如/var，进入

> cd /var/

创建swap文件，执行dd命令，增加一个1G的swap文件，根据Redhat公司的建议，Linux系统swap分区最适合的大小是物理内存的1-2倍

> dd if=/dev/zero of=swapfile bs=1024 count=1024000

也可以是：

> dd if=/dev/zero of=swapfile bs=1G count=1

这两条命令都是从硬盘里分出一个1G大小的空间，挂在swapfile上 接着再把这个分区变成swap分区

> /sbin/mkswap swapfile

并使其成为有效状态

> /sbin/swapon swapfile

检查是否正确

> free -m

或者

> /sbin/swapon -s

即可看到swap分区和大小以及使用情况 最后需要修改/etc/fstab 文件，使其可以随服务器重启时自动启动swap分区

> echo "/var/swapfile swap swap defaults 0 0" >>/etc/fstab

至此，已全部完成添加swap分区。