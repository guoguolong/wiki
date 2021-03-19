首先通过jinfo来查看tomcat所用到的VM参数

1. jps

查看tomcat进程的pid，比如pid为23045

1. jinfo

> jinfo -flag PermSize 23045
> jinfo -flag MaxPermSize 23045

分别查看两个VM参数当前的参数，如果设置值得太小，设置JAVA_OPTS，如：

> export JAVA_OPTS="$JAVA_OPTS -Xms1024m -Xmx1024m -XX:PermSize=128M -XX:MaxNewSize=256m -XX:MaxPermSize=256m"

然后重新运行$tomcat/bin/startup.sh