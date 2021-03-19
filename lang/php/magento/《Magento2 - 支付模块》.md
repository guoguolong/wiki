前提：假设你们已经熟悉了Magento2后台模块建立的基本方法，现在我们制作一个“支付宝直接到账付支付接口”模块，

## 模块典型目录结构

```
/Jam
  |_ /Alipay
         |_ /Block
             |_ Box.php
         |_ /Controller
              |_ Shop.php
         |_ /etc
             |_ modules.xml
             |_ /frontend
                 |_ routes.xml
         |_ /i18n
             |_ zh_CN.csv
         |_ /Helper
             |_ Data.php
         |_ /Model
             |_ Payment.php
         |_ /sql
         |_ /view
             |_ /frontend
                 |_ /layout
                    |_<route_id_of_routes.xml>_<controller_name>_<action_name>.xml 
                 |_ box.phtml // Block类对应的模版文件
```

## 配置文件

etc/adminhtml/system.xml

```xml
<?xml version="1.0"?>
<config>
    <system>
        <section id="payment" translate="label">
            <group id="alipay" translate="label" type="text" sortOrder="0" showInDefault="1" showInWebsite="1" showInStore="1">
                <label>Alipay</label>
                <field id="active" translate="label" type="select" sortOrder="1" showInDefault="1" showInWebsite="1" showInStore="0">
                    <label>Enabled</label>
                    <source_model>Magento\Backend\Model\Config\Source\Yesno</source_model>
                </field>
                <field id="title" translate="label" type="text" sortOrder="2" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>标题</label>
                </field>
                <field id="service_type" translate="label" type="select" sortOrder="3" showInDefault="1" showInWebsite="1" showInStore="0">
                    <label>Service Type</label>
                    <source_model>Jam\Alipay\Model\Config\Source\ServiceType</source_model>
                </field>
                <field id="gateway" translate="label" type="text" sortOrder="4" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Gateway</label>
                </field>
                <field id="partner_id" translate="label" type="text" sortOrder="5" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Partner ID</label>
                </field>
                <field id="security_code" translate="label" type="text" sortOrder="6" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Security Code</label>
                </field>
                <field id="seller_name" translate="label" type="text" sortOrder="7" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Seller Name</label>
                </field>
                <field id="seller_email" translate="label" type="text" sortOrder="8" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Seller Email</label>
                </field>
                <field id="transport" translate="label" type="select" sortOrder="9" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Transport</label>
                    <source_model>Jam\Alipay\Model\Config\Source\Transport</source_model>
                </field>
                <field id="sign_type" translate="label" type="text" sortOrder="12" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Signature Type</label>
                </field>
                <field id="input_charset" translate="label" type="text" sortOrder="13" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Input Charset</label>
                </field>
                <field id="redirect_url" translate="label" type="text" sortOrder="20" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Redirect Url(Callback)</label>
                </field>
                <field id="return_url" translate="label" type="text" sortOrder="22" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Return Url(Callback)</label>
                </field>
                <field id="notify_url" translate="label" type="text" sortOrder="24" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Notify Url(Callback)</label>
                </field>
            </group>
        </section>
    </system>
</config>
```

一般需要定义一个section标签到id属性为payment的标签下，并且section下必须有一个field的id属性为action为boolean类型.

etc/config.xml

```
<?xml version="1.0"?>
<config>
    <default>
        <payment>
            <alipay>
                <active>0</active>
                <title>Alipay</title>
                <model>Jam\Alipay\Model\Payment</model>
                <gateway>mapi.alipay.com/gateway.do</gateway>
                <partner_id>2088102654830159</partner_id>
                <security_code>2la9vgdc6u0tapqslj9msxgmaj0qn2b9</security_code>
                <seller_name>红山楂工作室</seller_name>
                <seller_email>jieheng.liang@gmail.com</seller_email>
                <transport>https</transport>
                <redirect_url>jamalipay/payment/redirect</redirect_url>
                <return_url>jamalipay/payment/return</return_url>
                <notify_url>jamalipay/payment/notify</notify_url>
                <sign_type>MD5</sign_type>
                <input_charset>utf-8</input_charset>
            </alipay>
        </payment>
    </default>
</config> 
```

这个文件给system.xml设置默认值，重要的：标签alipay必须定义到/config/default/payment标签下，下面必须有有一个model的标签，对应Hook的模型类。注意标签alipay的命名稍后如何被使用

## Payment类

```php
<?php
namespace Jam\Alipay\Model;
class Payment extends \Magento\Payment\Model\Method\AbstractMethod { //注意继承类
     protected $_code  = 'alipay'; // @必须与config.xml里payment标签的最近子标签保持一致.
     protected $_url             = null;
    public function __construct(
         \Magento\UrlInterface $url,
         \Magento\Event\Manager $eventManager,
         \Magento\Payment\Helper\Data $paymentData,
         \Magento\Core\Model\Store\ConfigInterface $coreStoreConfig,
         \Magento\Core\Model\Log\AdapterFactory $logAdapterFactory,
         array $data = array()
     ) {
        parent::__construct($eventManager , $paymentData , $coreStoreConfig , $logAdapterFactory);
        $this->_url = $url;
    }
       /**
      *  此回调方法. 当点击"下订单"按钮时，导向的页面URL.
      *  @return       string Order Redirect URL
      */
     public function getOrderPlaceRedirectUrl() {
          return $this->_url->getUrl($this->getConfigData('redirect_url'));
     }
} 
```

## 先测试一把

登录后台Stores->Configuration->Payment Methods看到右侧页面顶部显示如下

请设置第一个选项Enabled为Yes。然后从网店前端下一个订单，当进行chekcout到“payment methods”是多了一个radio选项“Alipay”，如果选中该选项，然后继续下一步，当最后点击”Place Order”时，将转入Payment->getOrderPlaceRedirectUrl()方法指向的URL，即上面system.xml配置的“RedirectUrl(Callback)”选项的值: jamalpay/payment/redirect。不过由于还没有定义该Action，页面将导向404 page。

不过，这个时候，你已经很接近成功了。

Magento2支付模块的基础就是这么简单。接下来让我们继续:

## Redirect url对应的Controller/Block类

### 1. Controller类

```php
<?php
namespace Jam\Alipay\Controller;
use Jam\Alipay\Model\Config\Source\ServiceType; // 定义了Alipay的几种服务类型常量。
use Magento\Sales\Model\Order;
class Payment extends \Magento\App\Action\Action {
    protected $_checkoutSession;
     protected $_order;
    public function __construct(
        \Magento\App\Action\Context $context,
        \Magento\Checkout\Model\Session $checkoutSession
    ) {
        $this->_checkoutSession = $checkoutSession;
        parent::__construct($context);
    }
    /**
     * 这个页面就是一个转向支付接口的过渡页面，这个页面准备了要提交到支付接口网站要提交的参数
     */
     public function redirectAction() {
          $this->_checkoutSession->setAlipayPaymentQuoteId($this->_checkoutSession->getQuoteId());
          $order = $this->getOrder();
          $order->addStatusToHistory($order->getStatus() , __('Customer was redirected to Alipay'));
          if (!$order->getId()) {
               $this->norouteAction();
               return;
          }
          $payment = $this->_objectManager->create('Jam\Alipay\Model\Payment');
          if($payment->getConfigData('service_type') == ServiceType::TYPE_CREATE_PARTNER_TRADE_BY_BUYER) {
               $order->setState(Order::STATE_PROCESSING, Payment::STATUS_PENDING_PAYMENT);
          } else if($payment->getConfigData('service_type') == ServiceType::TYPE_CREATE_DIRECT_PAY_BY_USER) {
               $order->setState(Order::STATE_PROCESSING);
          }
          $order->save();
          $this->getResponse()->setBody(
               $this->getLayout()
               ->createBlock('Jam\Alipay\Block\Redirect')
               ->setOrder($order)
               ->toHtml()
          );
          $this->_checkoutSession->unsQuoteId();
     }
} 
```

## 2. Block类

```php
<?php
namespace Jam\Alipay\Block;
use Jam\Alipay\Model\Config\Source\ServiceType;
class Redirect extends \Magento\Core\Block\Template {
    protected $_objectManager;
   
    public function __construct(\Magento\ObjectManager $objectManager,
        \Magento\Data\Form\Factory $formFactory,
        \Magento\Core\Helper\Data $coreData,
        \Magento\Core\Block\Template\Context $context,
        array $data = array()
         ) {
         parent::__construct($coreData , $context, $data);
        $this->_objectManager = $objectManager;
        $this->_formFactory = $formFactory;
    }
     protected function _toHtml() {
          $alipay = $this->_objectManager->create('Jam\Alipay\Model\DirectPayment'); // Default DirectPayment.
          if($alipay->getConfigData('service_type') == ServiceType::TYPE_CREATE_PARTNER_TRADE_BY_BUYER) {
               $alipay = $this->_objectManager->create('Jam\Alipay\Model\EscowPayment');
          }
          $alipay->setOrder($this->getOrder());

        $form = $this->_formFactory->create();
          $form->setAction($alipay->getGatewayUrl())
               ->setId('alipay-payment-checkout')
               ->setName('alipay-payment-checkout')
               ->setMethod('GET')
               ->setUseContainer(true);
          $form_fields = $alipay->getCheckoutFormFields();
          foreach ($form_fields AS $field => $value) {
               $form->addField($field, 'hidden', array('name' => $field, 'value' => $value));
          }

          $html = '<html>';
          $html.= '<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>';
          $html.= '<body>';
          $html.= __('You will be redirected to Alipay in a few seconds.');
          $html.= $form->toHtml();
          $html.= '<script type="text/javascript">document.getElementById("alipay-payment-checkout").submit();</script>';
          $html.= '</body></html>';

          return $html;
     }
} 
```

该类图方便，html直接在_toHtml方法里，当时，不建议大家这样使用，还是定义layout和phtml文件比较好 此时，重新下一个订单，点击“Place Order”就正确进入到这个页面了。

注：显然，我的说法是有问题的，比如上述代码中 $alipay->getCheckoutFormFields();没有定义，如何通过测试呢？所以，我前面只是想通过这个类框架来说明支付模块开发如何简单、清晰；为了让代码能够运行，解析来才真正进入到Alipay支付接口的技术细节

### 完善Controller文件

### 完善Model文件Payment