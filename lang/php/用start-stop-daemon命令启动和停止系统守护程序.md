Ubuntu是预装了start-stop-daemon，但是其他linux发行包是否内置该程序请自行检查.

## I. 参数含义

start-stop-daemon --help

```
Commands:
  -S|--start -- <argument> ...  开启一个系统守护程序，并传递参数给它
  -K|--stop                     停止一个程序
  -T|--status                   得到程序的状态
  -H|--help                     显示帮助信息
  -V|--version                  打印版本信息
  
Matching options (at least one is required):
  -p|--pidfile <pid-file>       pid file to check
  -x|--exec <executable>        program to start/check if it is running
  -n|--name <process-name>      process name to check
  -u|--user <username|uid>      process owner to check

Options:
  -g|--group <group|gid>        按指定用户组权限运行程序
  -c|--chuid <name|uid[:group|gid]>
                                按指定用户、用户组权限运行程序
  -s|--signal <signal>          signal to send (default TERM)
  -a|--startas <pathname>       program to start (default is <executable>)
  -r|--chroot <directory>       chroot to <directory> before starting
  -d|--chdir <directory>        change to <directory> (default is /)
  -N|--nicelevel <incr>         add incr to the process' nice level
  -P|--procsched <policy[:prio]>
                                use <policy> with <prio> for the kernel
                                  process scheduler (default prio is 0)
  -I|--iosched <class[:prio]>   use <class> with <prio> to set the IO
                                  scheduler (default prio is 4)
  -k|--umask <mask>             在开始运行前设置<mask> 
  -b|--background               后台运行
  -m|--make-pidfile             当命令本身不创建pidfile时，由start-stop-daemon创建
  -R|--retry <schedule>         等待timeout的时间，检查进程是否停止，如果没有发送KILL信号；
  -t|--test                     测试模式
  -o|--oknodo                   exit status 0 (not 1) if nothing done
  -q|--quiet                    不要输出警告
  -v|--verbose                  显示运行过程信息
```

## II. 应用例子1

### 1、开启一个daemon进程

```
start-stop-daemon --start --background --exec /project/daemon/dark.py
```

### 2、关闭一个daemon进程

```
start-stop-daemon --stop --name dark.py
```

## III. PHP应用例子

对于php程序，--stop --name 总是指定php才能停止进程，这导致所有php后台进程都被杀死，所以这里要借助--pidfile参数

### 1、开启一个daemon进程

```
start-stop-daemon --start --background -mp /tmp/black-daemon.pid --exec /projects/daemon/black-daemon.php
```

### 2、关闭一个daemon进程

```
start-stop-daemon --stop --pidfile /tmp/black-daemon.pid
```

注：start-stop-daemon --stop不能自动删除pid文件，所以可能需要手动清理pidfile

```
rm -f /tmp/black-daemon.pid
```