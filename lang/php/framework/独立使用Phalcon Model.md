独立使用Phalcon Model

```php
<?php
use Phalcon\Mvc\Model\Metadata\Memory;
class Feed extends \Phalcon\Mvc\Model {
    // const DB_DEFAULT = 'db';
    public function initialize()
    {
        $this->setConnectionService('dbMysql');
    }
    public function getSource()
    {
        return 'feed';
    }
}
$di = new \Phalcon\DI();
$di->set('modelsMetadata', function () {
    $metaData = new Memory();
    return $metaData;
});
$di->set('modelsManager', function() {
  return new \Phalcon\Mvc\Model\Manager();
});
$di->set('dbMysql', function() {
     return new \Phalcon\Db\Adapter\Pdo\Mysql(array(
        "host" => "localhost",
        "username" => "root",
        "password" => "",
        "dbname"   => "babyun_local"
    ));
});
$feed = Feed::findFirst(2);
print_r($feed->toArray());
```