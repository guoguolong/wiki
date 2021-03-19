df和du都查过，磁盘空间足够啊，原来要执行下面的命令

> echo 900000 | sudo tee /proc/sys/fs/inotify/max_user_watches

将max_user_watches文件里的值增大。