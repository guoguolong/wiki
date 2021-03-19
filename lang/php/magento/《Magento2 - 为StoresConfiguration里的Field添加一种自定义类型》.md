Magento2的Stores->Configuration中field设置在system.xml声明，如：

```xml
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
                    <label>Title</label>
                </field>
```

field的type可用类型有text、select、checkbox等，具体可用的所有类名是现在<magento2 installed folder>/lib/Magento/Data/Form/Element。 现在假设想定义一个两列的表格

I. 在etc/adminhtml/system.xml中声明该定制field，如：

```xml
<?xml version="1.0"?>
<config>
    <system>
        <section id="invoice" translate="label" type="text" sortOrder="102" showInDefault="1" showInWebsite="1" showInStore="1">
            <label>Invoice Information</label>
            <resource>Haw_Invoice::config_invoice</resource>
            <tab>haw</tab>
            <group id="invoice" translate="label" type="text" sortOrder="0" showInDefault="1" showInWebsite="1" showInStore="0">
                <label>Invoice Setting</label>
                <field id="content_type" translate="label" type="Jam\Invoice\Model\Form\Element\Grid" sortOrder="2" showInDefault="1" showInWebsite="1" showInStore="0">
                    <label>Invoice Content Type</label>
                </field>
            </group>
        </section>
    </system>
</config> 
```

II.定义Jam\Invoice\Model\Form\Element\Grid类

```php
<?php
/**
 * Form grid element
 *
 * @category   Jam
 * @package    Jam_Invoice
 * @author     Haw Studio Team <developer@hawstudio.com>
 */
namespace Jam\Invoice\Model\Form\Element;
class Grid extends \Magento\Data\Form\Element\AbstractElement {
    /**
     * @param \Magento\Escaper $escaper
     * @param \Magento\Data\Form\Element\Factory $factoryElement
     * @param \Magento\Data\Form\Element\CollectionFactory $factoryCollection
     * @param array $attributes
     */
    public function __construct(
        \Magento\Escaper $escaper,
        \Magento\Data\Form\Element\Factory $factoryElement,
        \Magento\Data\Form\Element\CollectionFactory $factoryCollection,
        $attributes = array()
    ) {
        parent::__construct($escaper, $factoryElement, $factoryCollection, $attributes);
    }
    public function getHtmlAttributes() {
        return array('title', 'class', 'style', 'onclick', 'onchange', 'rows', 'cols', 'readonly', 'disabled',
                     'onkeyup', 'tabindex');
    }
    public function getElementHtml() {
        $html = '';
        $rowCount = 4;
        $html .= '<table>';
        $html .= '<tr><th>' . __('Title') . '</th><th>' . __('Value') . '</th></tr>';
        for ($i = 0; $i < $rowCount; $i++) {
            $this->setStyle('width:100%');
            $fields = $this->getValue($i);
            $html .= '<tr class="grid-input">’;
            $this->setClass('input-text required-entry');
            $html .= '<td><input id="' . $this->getHtmlId() . $i . '-name" name="' . $this->getName()
                . '[' . $i . '][name]' . '" value="' . $this->_escape($fields[0]) . '" '
                . $this->serialize($this->getHtmlAttributes()) . '  ' . $this->_getUiId($i) . '/></td>’;
            $this->setClass('input-text required-entry validate-number');
            $html .= '<td><input id="' . $this->getHtmlId() . $i . '-value" name="' . $this->getName()
                . '[' . $i . '][value]' . '" value="' . $this->_escape($fields[1]) . '" '
                . $this->serialize($this->getHtmlAttributes()) . '  ' . $this->_getUiId($i) . '/></td>';
            $html .= '</tr>';
        }
        $html .= '</table>';
        return $html;
    }
} 
```

现在进入后台Stores->Configuration相应的Section就看如下的运行结果

此时还不能存储该Field。请继续

III. 在system.xml添加一个backend_model标签

```xml
<field id="content_type" translate="label" type="Jam\Invoice\Model\Form\Element\Grid" sortOrder="2" showInDefault="1" showInWebsite="1" showInStore="0">
    <comment>Please specify invoice particulars here. The value must be a integer greated than 1. '|' or ',' is now allowed in title fields.</comment>
    <label>Invoice Content Type</label>
    <backend_model>Jam\Invoice\Model\Backend\GridBackend</backend_model>
</field> 
```

该BackendModel类作用是为Configuration存储或载入时Hook一些前置和后置处理方法。首先我们要知道配置的每一条field都被存储到core_config_data表。对于本例名为content_type的field的值是表格数据，所以点击”Save Configuration”提交到后台的数据是数组，要序列化成字符串再存储到core_config_data.

III. 定义BackendModel类Jam\Invoice\Model\Backend\GridBackend

```php
<?php
namespace Jam\Invoice\Model\Backend;
class GridBackend
    extends \Magento\Core\Model\Config\Value
    implements \Magento\Core\Model\Config\Data\BackendModelInterface
{
    /**
     * @var \Magento\Encryption\EncryptorInterface
     */
    protected $_encryptor;
    /**
     * @param \Magento\Core\Model\Context $context
     * @param \Magento\Core\Model\Registry $registry
     * @param \Magento\Core\Model\StoreManager $storeManager
     * @param \Magento\Core\Model\Config $config
     * @param \Magento\Encryption\EncryptorInterface $encryptor
     * @param \Magento\Core\Model\Resource\AbstractResource $resource
     * @param \Magento\Data\Collection\Db $resourceCollection
     * @param array $data
     */
    public function __construct(
        \Magento\Core\Model\Context $context,
        \Magento\Core\Model\Registry $registry,
        \Magento\Core\Model\StoreManager $storeManager,
        \Magento\Core\Model\Config $config,
        \Magento\Encryption\EncryptorInterface $encryptor,
        \Magento\Core\Model\Resource\AbstractResource $resource = null,
        \Magento\Data\Collection\Db $resourceCollection = null,
        array $data = array()
    ) {
        $this->_encryptor = $encryptor;
        parent::__construct($context, $registry, $storeManager, $config, $resource, $resourceCollection, $data);
    }
    // 载入页面后，反序列化字符串为数组
     protected function _afterLoad() {
        $values = $this->getValue();
        $decoded_values = array();
        if($values) {
            $rows = explode('|' , $values);
            foreach ($rows as $row) {
                $decoded_values[] = explode(',' , $row);
            }
        }
        $this->setValue($decoded_values);
    }
    // 页面提交后，序列化数组为字符串供回调存储。
     protected function _beforeSave() {
        $values = $this->getValue();
        $encoded_values = array();
        foreach ($values as $row) {
            $encoded_values[] = implode(',' , $row);
        }
        if($encoded_values) $this->setValue(implode('|' , $encoded_values));
    }
    public function processValue($value) {
        return $values;
    }
} 
```

回头看看Grid.php的getElementHtml()，是如何通过 $fields = $this->getValue($i);把数据显示到项目上的。

BTW： 关于Source Model（即<field>标签下的<source_model>）仅仅适用于 type为сheckbox, multi select, radio or select的field.