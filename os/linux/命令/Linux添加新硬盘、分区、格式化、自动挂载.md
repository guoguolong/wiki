在为主机添加硬盘前，首先要了解Linux系统下对硬盘和分区的命名方法。

1） 在Linux下对SCSI的设备是以sd命名的，第一个ide设备是sda，第二个是sdb,依此类推。一般主板上有两个SCSI接口，一共可以安装四个SCSI设备。主SCSI上的两个设备分别对应sda和sdb，第二个SCSI口上的两个设备对应sdc和sdd。一般硬盘安装在主SCSI的主接口上，所以是sda或者sdb,光驱一般安装在第二个SCSI的主接口上，所以是sdc. (IDE接口设备是用hd命名的，第一个设备是hda，第二个是hdb。依此类推.)

2）分区是用设备名称加数字命名的。例如sda1代表sda这个硬盘设备上的第一个分区。

3）每个硬盘可以最多有四个主分区，一个扩展分区，扩展分区可以再分为多个逻辑分区。

如下为新加一个SCSI硬盘，分区为扩展分区，且只包含1个逻辑分区sdb1，然后格式化为ext3，挂载到/test，增加到/etc/fstab系统启动时自动挂：

## 1、给硬盘分区

```
fdisk /dev/sda
Command (m for help): n
Command action
e extended
p primary partition (1-4)
输入：e
Partition number (1-4): 1
First cylinder (1-9729, default 1):回车
Last cylinder or +size or +sizeM or +sizeK (1-9729, default 9729):回车
Command (m for help):w(保存退出)
```

## 2、格式化硬盘

```
fdisk -l
mkfs -t ext3 /dev/sda1
Writing superblocks and filesystem accounting information:直接回车。
```

## 3、挂载

```
mount /dev/sda1 /test
```

## ４、开机直接挂载

```
编辑/etc/fstab　文件
添加：/dev/sda1 /test ext3 defaults 1 1
重启则发选已经挂载上去。
```

如下为新加一个ide硬盘，分区为扩展分区，且只包含1个逻辑分区hdb1，然后格式化为ext3，最后挂载到/opt2：

```
[root]# fdisk /dev/hdb
Command (m for help): m     (Enter the letter "m" to get list of commands)
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
e
Partition number (1-4): 1
First cylinder (1-2654, default 1):
Using default value 1
Last cylinder or +size or +sizeM or +sizeK (1-2654, default 2654):
Using default value 2654
Command (m for help): p
Disk /dev/hdb: 240 heads, 63 sectors, 2654 cylinders
Units = cylinders of 15120 * 512 bytes
   Device Boot    Start       End    Blocks   Id  System
/dev/hdb1             1      2654  20064208+   5  Extended
Command (m for help): w    (Write and save partition table)
[root]# mkfs -t ext3 /dev/hdb1
mke2fs 1.27 (8-Mar-2002)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
2508352 inodes, 5016052 blocks
250802 blocks (5.00%) reserved for the super user
First data block=0
154 block groups
32768 blocks per group, 32768 fragments per group
16288 inodes per group
Superblock backups stored on blocks:

        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000
Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 34 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
[root]# mkdir /opt2
[root]# mount -t ext3 /dev/hdb1 /opt2
```

## 5. 自动挂载：

只要将分区信息写到 /etc/fstab 文件中即可实现系统启动的自动挂载，例如对于 /dev/xvdb 的自动挂载添加如下的行即可： /dev/xvdb /mnt/zeus ext3 defaults 0 0