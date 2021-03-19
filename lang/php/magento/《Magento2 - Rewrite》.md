Magento2的Rewrite用Dependency Injection处理.

- 重写的基础做法(Block类为例)

假设现在想再Jam_Order模块里定制用户登录页的“最近订单”这个Block，为每个订单条目增加一个action(在phtml中增加一个链接)。 通过全文查找layout文件，找到该类的的名字为Magento\Sales\Block\Order\Recent，对应的phtml模版文件为order/recent.phtml。 步骤如下：

1. 定义etc/di.xml

```xml
<?xml version="1.0"?>
<config>
    <preference for="Magento\Sales\Block\Order\Recent" type=“Jam\Order\Block\Order\Recent" />
</config> 
```

1. 定义类: Jam\Order\Block\Order\Recent

```
<?php
namespace Jam\Order\Block\Order;
class Recent extends \Magento\Sales\Block\Order\Recent {
    // 扩展了一个新的方法
    public function getPayUrl($order) {
        return $this->getUrl('jamorder/payment/redirect', array('order_id' => $order->getId()));
    }
} 
```

1. view/frontend/order/recent.phtml

```xml
<?php echo $this->getAction();?>
     <a href="<?php echo $this->getPayUrl($_order) ?>" class="action pay">
        <span><?php echo __('Go to pay') ?></span>
    </a>
<?php endif ?>
```

再次进入客户登录首页，“最近订单”就别替换为重写过的Jam\Order\Block\Order\Recent 思考：如果di.xml文件定义了多个对同一个类的重写的类，那么谁会生效？

```xml
<?xml version="1.0"?>
<config>
    <preference for="Magento\Sales\Block\Order\Recent" type=“Jam\Order\Block\Order\Recent" />
    <preference for="Magento\Sales\Block\Order\Recent" type=“Jam\Order\Block\Order\Recent2" />
</config>
```

答案是：谁被最后载入，谁生效。对于上面的例子，Jam\Order\Block\Order\Recent2类将生效。 如果也想为重写Block类定义一个新的phtml文件，那么方法是重新定义layout。当然这个是重写phtml的机制，可单独使用(就是在那些仅仅需要定制模版文件的场合使用)。对于上面的例子，首先找到Recent对应的layout文件，即customer_account_index.php，将该文件建立在模块view/frontend/layout目录下，内容如下：

```xml
<?xml version="1.0"?>
<layout xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <referenceBlock name="my.account.wrapper">
        <block class="Jam\Alipay\Block\Order\Recent" name="customer_account_dashboard_top" after="customer_account_dashboard_hello" template="order/recent2.phtml"/>
    </referenceBlock>
</layout> 
```

需要注意的是：上述代码的block class可以定义为：Jam\Alipay\Block\Order\Recent或者Magento\Sales\Block\Order\Recent

- 重写Controller的Action

和前面一样，比如需要重定义Magento\Adminhtml\Controller\Sales\Order\Shipment的newAction方法，di.xml内容如下:

```xml
<?xml version="1.0"?>
<config>
    <preference for="Magento\Adminhtml\Controller\Sales\Order\Shipment" type="Jam\Alipay\Controller\Adminhtml\Sales\Order\Shipment" />
</config> 
```

Jam\Alipay\Controller\Adminhtml\Sales\Order\Shipment内容:

```php
<?php
namespace Jam\Alipay\Controller\Adminhtml\Sales\Order;
class Shipment extends \Magento\Adminhtml\Controller\Sales\Order\Shipment {
     // 重定义newAction方法
    public function newAction() {
         parent::newAction();
         // add your own logic.
    }
} 
```