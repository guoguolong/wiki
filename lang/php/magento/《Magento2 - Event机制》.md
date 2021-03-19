## 1. 定义事件名称及对应的观察者(Observer)类和方法

etc/events.xml

```xml
<?xml version="1.0"?>
<config>
    <event name="catalog_model_product_duplicate">
        <observer name="downloadable_observer" instance="Jam\Shop\Model\Product\Observer" method="duplicateProduct" />
    </event>
</config> 
```

## 2. 事件派遣

```php
// 在某Controller类中
$this->_eventManager->dispatch(
  'catalog_model_product_duplicate',
  array('current_product' => $this, 
    'new_product' => $newProduct
  )
); 
```

## 3. 监听器类

```php
<?php
namespace Jam\Shop\Model\Product;

class Observer {
    public function duplicateProduct($observer)
    {
        $currentProduct = $observer->getEvent()->getCurrentProduct();  // 该方法与事件派遣参数current_product对应
        $newProduct = $observer->getEvent()->getNewProduct(); // 该方法与事件派遣参数new_product对应
        // ….. More Logic.
     } 
}
```

此时，如果某个页面执行了事件派遣，将触发 Jam\Shop\Model\Product\Observer->duplicateProduct()的执行.