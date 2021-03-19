php有针对signal的处理模块PCNTL，检查自己的php环境有否该扩展，然后运行下面代码：

## black-daemon.php

```php
#!/usr/bin/env php
<?php

// 判断是非windows操作系统，注册信号处理.
if(PATH_SEPARATOR==':') {
    pcntl_signal(SIGTERM, "sig_handler");
    pcntl_signal(SIGHUP,  "sig_handler");
    pcntl_signal(SIGINT,  "sig_handler");
}

$exit_flag = false;
function sig_handler($signo) {
    global $exit_flag;
    echo "Signal handling......";
    switch ($signo) {
        case SIGTERM : // service stop或者kill <proc> 都会发出该信号
        case SIGHUP :
        case SIGINT :
            $exit_flag = true;
            break;
        default :
            // handle all other signals
    }
}

declare(ticks = 1); // 使signal设置生效
$target_file = __DIR__ . '/d.log';
while(true) {
    file_put_contents($target_file , time() . PHP_EOL , FILE_APPEND);
    $i = 0;
    // 该循环是一个完整事务,当捕获到linux几个注册的信号后，至少要等到该子循环完毕才检查进行相应处理.
    while($i < 10) {
        file_put_contents($target_file , '    -' .$i . ':' . time() . PHP_EOL , FILE_APPEND);
        sleep(1);
        $i++;
    }
    if($exit_flag) {
        break;
    }
    sleep(1);
}
file_put_contents($target_file , '------ Done --------' . PHP_EOL , FILE_APPEND);
```