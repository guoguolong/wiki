前提：如果以root用户启动nginx，通过ps查看work process使用以nobody用户启用。

下面有一个网站存储在/projects/chaikr.com，为了防止其他用户看到内容，正确设置权限方法是：使该目录属组更改为nobody，并赋予其完全权限，相应组赋予rx，其他人无任何权限，root登录后执行如下命令：

> chown -R nobody:nobody /projects/chaikr.com
> chmod -R 750 /projects/chaikr.com

执行ls -l /projects/检查chaikr.com子目录的权限如下：

> drwxr-x--- 2 nobody nobody 4096 5月 21 14:30 [chaikr.com](http://chaikr.com)