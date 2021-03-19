可以通过start-stop-daemon程序使脚本运行在后台，参考

>《用start-stop-daemon命令启动和停止系统守护程序.md》
>《PHP后台程序对LINUX信号的处理.md》

描述的原理，另外要在black-daemon.php头部增加代码：

```php
// 设定进程名字(需要pecl扩展proctitle)
$daemon_name = 'any-daemon';
if(!empty($argv[1])) {
    $daemon_name = $argv[1];
}
cli_set_process_title($daemon_name);
```

编写一个shell脚本(这里命名为black-daemon)设置可执行模式，然后复制到/etc/init.d/black-daemon，这样既可以执行

service black-daemon回显：

> Usage: black-daemon {start|stop|restart|status}

### 附：black-daemon脚本代码如下：

```bash
PID=/tmp/black-daemon.pid
PNAME=black-daemon
DAEMON=/projects/daemon/black-daemon.php
DAEMON_OPTS=$PNAME
do_start()
{
    if [ `pidof $PNAME` ] ; then
        echo "start: $PNAME is already running"
        return 1
    fi
    start-stop-daemon --start --background -mp $PID --exec $DAEMON -- $DAEMON_OPTS 2>/dev/null
    echo "$PNAME start/running"
    return 0
}
do_stop()
{
    if [ -z `pidof $PNAME` ] ; then
        echo "stop: $PNAME is already stopped"
        return 1
    fi
    start-stop-daemon --stop --pidfile $PID 2>/dev/null
    rm -f $PID
    echo "$PNAME stop/waiting"
    return 0
}

do_status()
{
    if [ `pidof $PNAME` ] ; then
        echo "status: $PNAME is already running"
    else
        echo "status: $PNAME is already stopped"
    fi
    return 0
}

case "$1" in
    start)
        do_start
        ;;
    stop)
        do_stop
        ;;
    status)
        do_status
        ;;
    restart)
        do_stop
        do_start
        ;;
    *)
        echo "Usage: $PNAME {start|stop|restart|status}" >&2
        exit 3
        ;;
esac
```