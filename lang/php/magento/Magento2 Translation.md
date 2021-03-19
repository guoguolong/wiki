官网翻译项目： https://crowdin.com/project/magento-2

一般的翻译方法（getpocket也有收藏）： http://blog.landofcoder.com/magento-2-translation/

如果用composer的方法翻译，要注意：安装完了要使用类似命令：

> php bin/magento cache:clean php bin/magento setup:static-content:deploy fr_FR

预生成翻译好对的模板，如果发现csv文件有错误需要修改，修改后需要再次执行上述命令.