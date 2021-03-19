本文中，将介绍在目前软件工程中经常用到的持续集成概念，并且会介绍在PHP开发中，如何能用好PHP目前开源的一些持续集成管理工具，去管理好项目。

持续集成的概念

持续集成的概念是在现代软件工程中提出的，最早见于敏捷开发方法论中，大师Martin Fowler对持续集成是这样定义的:持续集成是一种软件开发实践，即团队开发成员经常集成它们的工作，通常每个成员每天至少集成一次，也就意味着每天可能会发生多次集成。每次集成都通过自动化的构建(包括编译，发布，自动化测试)来验证，从而尽快地发现集成错误。许多团队发现这个过程可以大大减少集成的问题，让团队能够更快的开发内聚的软件。

## PHPUNIT

首先，PHPUNIT是PHP中的单元测试利器，项目地址在：http://www.phpunit.it。它

能自动运行你编写的单元测试代码，并给出是否通过的结果。安装步骤如下，可以使用PHP中的PEAR安装：

```
sudo apt-get install php5-curl php-pear php5-dev  
sudo pear upgrade pear  
sudo pear channel-discover pear.phpunit.de  
sudo pear channel-discover components.ez.no  
sudo pear channel-discover pear.symfony-project.com
sudo pear install phpunit/PHPUnit 
```

之后，就可以在命令行下，以如下格式执行phpunit：

Phpunit 单元测试的php文件名.php

此外，还可以执行如下命令，生成单元测试的覆盖报告：

phpunit --coverage-html ../CodeCoverage

## PHP CodeSniffer

PHP CodeSniffer(http://pear.php.net/package/PHP_CodeSniffer/redirected)是一个PHP的代码风格检测器，它根据预先设定好的PHP编码风格和规则，去检查应用中的代码风格情况，内置了ZEND，PEAR的编码风格规则，当然开发者也可以进行自定义

## PHP Depend

PHP Depend(http://pdepend.org/)是一个PHP中静态代码分析的工具。它可以用来检查你的PHP项目中的代码规模和复杂程度。

## PHP MESS DECTOR

PHP MESS DECTOR(简称PMD,项目地址http://phpmd.org/)，是基于pdepend的结果进行分析，分析出一旦你的PHP项目超过了pdepend中各具体指标值的规定，从而发出警告提示信息

## PHP COPY PASTE DETECTOR

Php copy paste detector(https://github.com/sebastianbergmann/phpcpd)是重构的一个好工具，它用来发现你的项目中的重复代码。

安装方法如下：

```
sudo pear channel-discover pear.phpunit.de
sudo pear channel-discover components.ez.no
sudo pear install phpunit/phpcpd
```

注意，必须先安装phpunit。

## PHP DEAD CODE Detector

php dead code detector(https://github.com/sebastianbergmann/phpdcd)是一个检查你的项目中有哪些代码是从来没被调用过的，比如类，方法编写后再没被调用过，这是一个去掉“坏味道”代码的最佳实践，可以增强系统的可维护性。安装如下：

```
sudo pear channel-discover pear.phpunit.de
sudo pear channel-discover components.ez.no
sudo pear install phpunit/phpdcd-beta
```