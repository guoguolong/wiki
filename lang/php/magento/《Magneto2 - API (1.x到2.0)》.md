1. 获得checkout 的session数据(Controller中）
   - 1.x $session = Mage::getSingleton('checkout/session');
   - 2.x $session = $this->_objectManager->get('Magento\Checkout\Model\Session');
2. 抛出异常 Controller中
   - 1.x Mage::throwException( __('There are no authentication credential storage.') );
   - 2.x self::throwException( __('There are no authentication credential storage.') ); Model中:
   - 2.x throw new \Magento\Core\Exception(__('Please define a correct Cookie instance.'));
3. 本地化字符串(Controller)
   - 1.x Mage::helper('alipay')->__('xxxxxx');
   - 2.x __('xxxxx');
4. 类Varien_Object被\Magento\Object替换
5. Model的存取(Controller) Model类和其他任何类的实例创建方法一样：用
   - 1.x Mage::getModel('xx/yy');
   - 2.x $this->_objectManager->get('Magento\Catalog\Helper\Product');
6. 获得扩展模块内部的资源文件（Controller , Helper文件）
   - 2.x $url = $this->_viewUrl->getViewFileUrl('Magento_Catalog::images/product/placeholder/image.jpg');
7. 获得Store信息
   - 2.x $curr_store_id = $this->_storeManager->getStore()->getId(); $storeModel = $this->_storeManager->getStore($curr_store_id); $storeModel->->getConfig('general/locale/code'); [//获得config.xml里标签的数据](http:////xn--config-vd3lr00v.xn--xml-ry6fr2gwsf2p7ag1iis4c)
8. 获得当前店铺的货币设置
   - 2.x $this->_storeManager->getStore()->getCurrentCurrencyCode();
9. 取得后台的Session
   - 2.x
     Controller Class: $session = $this->_objectManager->get('Magento\Adminhtml\Model\Session');
10. JSON编码
    - 1.x Mage::helper('core')->jsonEncode($response);
    - 2.x $this->_coreData->jsonEncode($response);
11. 模板(template)文件里显示css,js,image
    - 1.x <?php echo $this->getSkinUrl('images/alipay.gif');?>
    - 2.x <?php echo $this->getViewFileUrl(‘<Module Name>::images/alipay.gif') ?>
12. 在Block类用代码创建一个子Block，并调用返回输出，例子如下 public function getTradeNotifiesHtml() { $this->setChild('trade_notifies', $this->getLayout()->createBlock('Jam\Alipay\Block\Trade\Notifies')->setOrder($this->getOrder())); return $this->getChildBlock('trade_notifies')->toHtml(); } 上述代码创建了名字为Jam\Alipay\Block\Trade\Notifies的Block类，setChild方法使其设置为当前Block的子block，然后调用getChildBlock返回刚才创建的Block类
13. Log 例子： $this->_logger->log(’String to log', \Zend_Log::ERR); // 记录到var/log/system.log $this->_logger->logException($e); // 记录到var/log/exception.log 为了上述代码工作，需要进入后台Stores->Configuration->Advanced::Developer->Log Setting,设置Enabled为true. 注：如果Enabled没有设置为true，系统产生的一些异常也会记录到system.log中.
14. 非自增量主键发现不能使用”_"隔开三个单词，否则插入值总是为空. 比如主键trade_notify_id，存储该字段为空字符串.
15. 如果订单成功页面checkout/onepage/success/ 是检测Order的state不是Order::STATE_PROCESSING，发现页面显示：Your order # is: <order_id> 这里的order_id没有链接到“我的订单.” , 当然，只有定制了”Place Order”改变了默认STATE流转才出现这种情况，否则state此时默认是Order::STATE_PROECSSING.
16. 获得当前Url字符串 在Block或Controller中嵌入\Magento\Core\Helper\Url $urlHelper 调用 $urlHelper->getCurrentUrl();
17. 输出控制器名、动作方法名、路由名、模块名 Contrlloer和Block类都可以调用方法getRequest()，然后获得以上值，如： $this->getRequest()->getModuleName(); $this->getRequest()->getControllerName(); $this->getRequest()->getActionName(); $this->getRequest()->getRouteName();
18. 获得Url及使用 方法I. 注入\Magento\ObjectManager $objectManager对象，然后创建'Magento\Core\Model\Url对象 // 如果下述代码在Controller类中，_objectManager属性已经存在，不需要重复注入 $urlBuilder = $this->_objectManager->create('Magento\Core\Model\Url');
    $route = '*/*/edit’; $params = array('id' => $address->getId()); $urlBuilder->getUrl($route, $params); 方法II. 直接注入\Magento\UrlInterface对象到当前类的属性如$_url; $this->_url->getUrl('sales/order/view' , array('order_id’=>3))

可以将上述方法包装为方法 protected function _buildUrl($route = '', $params = array()) { /** @var \Magento\Core\Model\Url $urlBuilder */ $urlBuilder = $this->_objectManager->create('Magento\Core\Model\Url'); return $urlBuilder->getUrl($route, $params); } 对于Block类，可直接使用如下API $this->getUrl('customer/account/‘); //或者 $this->_urlBuilder->getUrl('hawinvoice/invoice/formPost', array('id’=>123)); 一旦获得了Url，常常要转向目标页面，写法有(在Controller文件中) $url = $this->_buildUrl('catalog/*/', array(‘param' => $param_value)); $this->getResponse()->setRedirect($ur);

// 如果参数为空，默认读取get参数success_url $success_url = $this->_redirect->success($url); $this->getResponse()->setRedirect($success_url);

// 如果参数为空，默认读取get参数error_url $error_url = $this->_redirect->error($url); $this->getResponse()->setRedirect($error_url);

// 默认读取get参数referer_url，否则读取Base64编码的get参数r64，否则读取url_encode编码的get参数uenc $referer_url = $this->_redirect->getRefererUrl(); $this->getResponse()->setRedirect($referer_url);