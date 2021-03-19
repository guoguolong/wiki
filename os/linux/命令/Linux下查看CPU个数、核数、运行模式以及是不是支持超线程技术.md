物理CPU与逻辑CPU的关系如下： 逻辑CPU数量=物理cpu数量 x CPU CORES x 2(如果支持并开启HT)

## 1.查看物理CPU个数

$> cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l 执行结果：2

## 2.查看逻辑CPU个数

$>cat /proc/cpuinfo |grep "processor"|wc -l 执行结果：8

## 3.查看单个CPU的核数

$>cat /proc/cpuinfo |grep "cores"| uniq 执行结果：4

## 4.是否开启intel的超线程技术(HT)

如果有两个逻辑CPU具有相同的"core id"，那么超线程是打开的。可以根据以下原则,来判断是否支持HT技术。 如果"siblings"和"cpu cores"一致，则说明不支持超线程，或者超线程未打开。 如果"siblings"是"cpu cores"的两倍，则说明支持超线程，并且超线程已打开。

$>cat /proc/cpuinfo |grep "sibling"|uniq 执行结果：siblings : 4

$>cat /proc/cpuinfo | grep "cpu cores"|uniq 执行结果：cpu cores : 4

## 5.CPU是32还是64位运行模式

$>getconf LONG_BIT 执行结果：64

注意：如果结果是32，代表是运行在32位模式下，但不代表CPU不支持64bit。 $>cat /proc/cpuinfo | grep flags | grep 'lm' | wc -l 执行结果：8 (结果大于0, 说明支持64bit计算. lm指long mode, 支持lm则是64bit)

如下执行结果表示未开启ht：逻辑CPU数量=物理cpu数量 * cpu cores

## 附：常用linux命令

### 1.wc命令

wc -c filename：显示一个文件的字节数 wc -m filename：显示一个文件的字符数 wc -l filename：显示一个文件的行数 wc -L filename：显示一个文件中的最长行的长度 wc -w filename：显示一个文件的字数

### 2.uniq命令

这个命令读取输入文件，并比较相邻的行。在正常情况下，第二个及以后更多个重复行将被删去，行比较是根据所用字符集的排序序列进行的。该命令加工后的结果写到输出文件中。输入文件和输出文件必须不同。如果输入文件用“- ”表示，则从标准输入读取。