free相对于top命令提供了更好的查看系统内存使用情况（默认单位是kb）：

```
[root@kaulware] free
            total      used      free    shared    buffers    cached
Mem:      3921316    2222560    1698756          0    169260    697504
-/+ buffers/cache:    1355796    2565520
Swap:            0          0          0
```

结果解释：

### 第一行 Mem:

```
- total:  物理内存总大小
- used: 指的是现在系统内核控制的内存数，即总计分配给缓存(包含buffers和cached)使用的数量，但其中包括未使用的buffer和cached
- free:  指的是未分配的内存，即现在系统内核还未纳入其管控范围的内存数。
- shared: 共享内存
- buffers: 系统分配但未被使用的buffers数量。
- cached: 系统分配但未被使用的cache数量。
```

### 第二行 -/+ buffers/cache:

```
- used: 实际使用的buffers 与cache 总量，也是实际使用的内存总量。
- free:  未被使用的buffers 与cache 和未被分配的内存之和，这就是系统当前实际可用内存。
```

### 第三行：Swap:

```
这里未设置，暂不解释.
```

显然第二行两个数据可以通过第一行计算出来： 实际使用内存总量 = 第一行used - 第一行buffers - 第一行cached 实际可用内存总量 = 第一行free + 第一行buffers + 第一行cached 总物理内存 = 实际使用内存总量 + 实际可用内存总量。