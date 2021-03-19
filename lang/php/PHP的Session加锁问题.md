当两个php页面都调用session_start 的时候，在使用相同的session id的情况下，如果一个页面在执行中,另一个页面需要等待前一个页面执行完才会执行session_start 后面的内容。这是并发导致的session加锁导致的. 举例： 1.php

```php
<?php 
    session_start(); 
    echo time(); 
    sleep(10); 
```

2.php

```php
<?php 
    echo time(); 
    session_start(); 
    echo time(); 
```

先执行1.php 然后马上执行2.php

会看到 2.php的 2个输出的time间隔 刚好是1.php 中sleep的10秒.

要解决这个问题 需要在1.php sleep 前执行session_write_close() 或者session_commit() 来结束对session的操作.

这样2.php session_start 后面的内容就无需等待1.php执行完才执行了

使用不同的session name 或者session id 都可以解决这个问题..具体的解决方案要视情况而定.这种情况有可能出现在例如 用php输出下载文件,当用户下载一个比较大的文件时,如果输出文件的php使用了session_start那么在文件输出完毕之前,用户将无法访问网站其他的网页,都会显示等待中.