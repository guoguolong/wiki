## 查找JAVA_HOME

1. 打开Mac的终端，检查JDK是否安装成功：java -version
2. 查看java指令所在的目录：which java。 输出结果：/usr/bin/java
3. 显示java指令文件的属性：ls -l /usr/bin/java。 输出结果如下： lrwxr-xr-x 1 root wheel 74 12 2 06:44 /usr/bin/java -> /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java，从输出结果可以知道**/usr/bin/java文件是一个链接文件，实际是指向/System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java**文件的。
4. 进入实际指令所在的文件夹： cd /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands。但是这个目录并不是JAVA_HOME目录。
5. 然后就是最重点的地方，在这个目录下面有一个mac的JDK特有的java_home指令可以查看JDK的JAVA_HOME目录。 执行指令：./java_home 执行结果如下： /Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home

## 设置JAVA_HOME

记得切换成root用户(sudo -i)或者给指令添加sudo

## 临时有效（重启后失效）

1. 编辑.bash_profile文件：vim ~/.bash_profile
2. 添加以下内容：

> export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home export PATH=$JAVA_HOME/bin:$PATH

1. 使修改的文件生效：source ~/.bash_profile

## 永久有效

1. 修改文件操作权限：chmod 773 /etc/profile
2. 编辑/ect/profile文件：vim /etc/profile
3. 添加以下内容：

> 

export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home export PATH=$JAVA_HOME/bin:$PATH

1. 使修改的文件生效：source /etc/profile