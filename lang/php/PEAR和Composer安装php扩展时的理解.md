PEAR安装好之后，把pear命令所在的目录加入到path；同理，composer 命令把vendor/bin添加到path，那么接下来安装扩展包的时候都会把执行文件安装到相应的目录下。

## 1. composer例

> composer global require ‘phploc/phploc=*’

二进制程序被安装到 /.composer/vendor/bin目录，然后将其加入该目录到path（/.bash_profile）

## 2. pear例

> pear install PHP_CodeSniffer

相关命令 phpcbf，phpcs 则被安装到pear的命令所在目录，可以直接运行