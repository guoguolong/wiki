先给出结论: 如果你使用FastCGI这种技术提供php访问，不需要考虑线程安全，只需要下载非线程安全的PHP包即可。

PHP自身是不支持线程的，但是它在安装的时候，涉及到一个线程安全的问题，Windows下提供了二种安装包，Linux下编译安装提供了–enable-maintainer-zts这个选项。

很多人一看到“安全”，就以为是好事，其实不然。 既然PHP没有线程，那么这个线程安全指的是什么呢?这和它的运行方式有关。

- php-fpm - 不需线程安全包 不涉及到线程安全这个问题了，因为php-fpm是以多进程的方式来运行的。php-fpm是一个优秀的FastCGI管理器
- Apache的mod_php - 需线程安全包 那么应该先了解下Apache的MPM，简单点说，Apache支持以多线程的方式运行(Worker)，也支持以多进程的方式运行(Prefork)。一般来讲，Linux下的Apache绝大多数都是运行在Prefork模式下，这是出于稳定性的考虑。
- ISAPI(Windows) - 需线程安全包 Windows下一般我们会把PHP配置成以ISAPI的方式来运行在IIS之上，ISAPI是多线程的方式。所以，如果使用ISAPI的方式来运行PHP就必须用Thread Safe(线程安全)的版本

PHP安装为线程安全，会比非线程安全多占用一些CPU，并且可能会增加bug或者不稳定的问题，这才是重点，不然PHP就没必要设置这个选项了。所以应尽量使用非线程安全的Php运行为php-fpm或者FastCGI模式