1. etc/module.xml 生命正确的版本号

```xml
<?xml version="1.0"?>
<config>
    <module name=“Jam_Shop" version="2.0.0.0" active="true">
    </module>
</config> 
```

1. sql/<module_name>_setup/目录下创建install-2.0.0.0.php

```php
 <?php
$installer = $this;
$installer->startSetup();
if (!$installer->getConnection()->isTableExists($installer->getTable('jam_sales_flat_quote_invoice'))) {
    $table = $installer->getConnection()
        ->newTable($installer->getTable('jam_sales_flat_quote_invoice'))
        ->addColumn('invoice_id', \Magento\DB\Ddl\Table::TYPE_INTEGER, null, array(
            'identity'  => true,
            'unsigned'  => true,
            'nullable'  => false,
            'primary'   => true,
            ), 'Invoice ID')
        ->addColumn('type', \Magento\DB\Ddl\Table::TYPE_INTEGER, null, array(
            'unsigned'  => true,
            'nullable'  => true,
            'default'   => 1,
            ), 'Invoice Type')
        ->addColumn('title', \Magento\DB\Ddl\Table::TYPE_TEXT, 100, array(
            'nullable'  => true,
            'default'   => null,
            ), 'Invoice Title')
        ->setComment('Invoice Table');
    $installer->getConnection()->createTable($table);
}
$installer->endSetup(); 
```

上面就是一个例子，根据自己需要修改 刷新magento网站任何一个页面（可能要清除缓存先），将执行install-2.0.0.0.php的sql更新

1. 接下来如果etc/module.xml声明version=3.0.0.0，那么sql/<module_name>_setup目录下创建：upgrade-2.0.0.0-3.0.0.0.php,内容根据需要写(无非SQL操作)。再次刷新magento任何一个页面(可能要清除缓存先)，Magento将检查core_setup表里该模块当前的版本，如果发现该版本为2.0.0.0，