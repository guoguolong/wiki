典型的Web页面校验是指表单的校验，一般分为客户端和服务器端校验，Magento2也不例外。

## 1. 客户端校验

pub/lib/mage/validation.js是客户端校验实现的核心代码。假设有如下代码

```xml
<form id="invoice-info-form" class="form invoice-info edit" action="http://magento2.me/hawinvoice/invoice/formPost/id/100/" method="post" >
    <input type="text" title="发票抬头" name="invoice[title]" value="" class="required-entry" / >  
    <button type="submit" ><span > 保存</span > </button>
 </form>
head.js(
    "<?php echo $this->getViewFileUrl('jquery/jquery.validate.js')?>",
    "<?php echo $this->getViewFileUrl('jquery/jquery.metadata.js')?>",
    "<?php echo $this->getViewFileUrl('mage/validation.js')?>",
    "<?php echo $this->getViewFileUrl('mage/validation/validation.js')?>",
    function() {
        jQuery('#invoice-info-form').validation();
    }
); 
```

点击”保存”按钮之后，form表单中input的class标记为如下字符串的都将被检验：

![image](http://note.youdao.com/yws/res/1104/89C6DA81162144C0A305EF3FCFF3CB33?ynotemdtimestamp=1616139625043)

一旦校验通过就将提交表单到后台。 如果校验成功不想提交表单，比如想送出一个AJAX请求：

```php
head.js(
    "<?php echo $this->getViewFileUrl('jquery/jquery.validate.js')?>",
    "<?php echo $this->getViewFileUrl('jquery/jquery.metadata.js')?>",
    "<?php echo $this->getViewFileUrl('mage/validation.js')?>",
    "<?php echo $this->getViewFileUrl('mage/validation/validation.js')?>",
    function() {
        if(jQuery('#invoice-info-form').validation(‘isValid’)) {
             // Send Ajax Request.
        }
    }
);  
```

## 2. 服务器校验

服务器端校验大的方面分逻辑校验和安全校验。当数据提交到后台时，在Controller的Action方法里调用或直接完成这些校验。

**A. 逻辑校验：**

Action方法里可以先做些基本的错误判断，然后对模型进行更细致的校验。模型校验分两类

### 1). “Data Models"(普通模型)校验

所谓”Data Models”是相对于”EAV Models”来说的，也就是说，除了Eav model，其他模型的校验都使用本规则：简单地使用Zend_Validate类进行校验。看下面的例子：

```php
\Magento\Review\Model\Revieve.php
     public function validate()    {
        $errors = array();
        if (!\Zend_Validate::is($this->getTitle(), 'NotEmpty')) {
            $errors[] = __('The review summary field can\'t be empty.');
        }
        if (!\Zend_Validate::is($this->getNickname(), 'NotEmpty')) {
            $errors[] = __('The nickname field can\'t be empty.');
        }
        if (!\Zend_Validate::is($this->getDetail(), 'NotEmpty')) {
            $errors[] = __('The review field can\'t be empty.');
        }
        if (empty($errors)) {
            return true;
        }
        return $errors;
    }
```

\Zend_Validate到底支持哪些校验规则，可查看lib/Zend/Validate/目录下的类名。 在Magento\Review\Controller\Product.php节选代码

```php
public function postAction() {
    $data   = $this->getRequest()->getPost();
    if (!empty($data)) { // 这里相当于简单的校验
        $review     = $this->_reviewFactory->create()->setData($data);
        $validate = $review->validate(); // 这里就是进一步校验模型类的属性合法性
        if ($validate === true) {
            ... ...
        } else {
            ... ...
        }
    }
    $this->_redirectReferer();
}
```

### 2). “EAV Model”(EAV模型)校验

EAV模型用到类\Magento\Eav\Model\Form来辅助校验，不过貌似目前只有Magento\Customer\Model\Form集成它并利用了这一特性。Magento\Customer\Model\Form通过构造器注入Factory来创建，看下面的Magento\Checkout\Model\Type\Onepage代码片段：

```php
// 目标：$addressForm校验传入数据($data数组) ，如果通过,赋值给目标实体类($address对象)，进一步操作(存储实体等)。
$data = array(….);
$address = $this->getQuote()->getBillingAddress();   

$addressForm = $this->_customerFormFactory->create();
$addressForm->setEntity($address); // 绑定实体对象到$addressForm

// 清理输入参数 ，只保留$address属性对应的数据，返回$addressData
$addressData    = $addressForm->extractData($addressForm->prepareRequest($data)); // prepareRequest转换输入数组为Http对象.

// 执行校验，校验规则是在EAV属性创建时指定的
$addressErrors  = $addressForm->validateData($addressData);

if ($addressErrors !== true) {
   return array('error' => 1, 'message' => array_values($addressErrors));
}
// 如果校验成功，将$addressData 赋值给目标实体 $address的对应属性
$addressForm->compactData($addressData); 
```
B. 安全校验：
主要是表单重复提交攻击的屏蔽，这要求在前页的phtml文件里增加如下红色代码

```html
<form id="invoice-info-form" class="form invoice-info edit" action="http://magento2.me/hawinvoice/invoice/formPost/id/100/" method="post" >
    <?php echo $this->getBlockHtml('formkey')?> 
</form>
```
这段红色的代码终将生成类似下面的结果：
```html
<input name="form_key" type="hidden" value="HFpmcYetLNmK16lg" /> 
```
在提交的目标Controller.action方法中，添加类似下面的代码

```php
// 注入\Magento\Core\App\Action\FormKeyValidator $_formKeyValidator
 if (!$this->_formKeyValidator->validate($this->getRequest())) {
       $this->_redirect('*/*/edit');
       return;
}
```

## 3. 保持表单数据

在表单提交后如果出现错误项，希望返回表单页面，并保持刚才输入的数据，这是一个常见的需求，这个可通过$_session->setFormData()数据，然后在回到表单页面取出后，清除SESSION

- 目标Controller.action方法

```php
$data = $this->getRequest()->getPost();
...
 if ($validate !== true) {
    $this->_customerSession->setFormData($data);  // 存储提交的数据到session
    $this->_redirectReferer(); // 回到表单页
 }
```

- 在表单的Block类中可以

```php
public function getDataForForm() {
    if($this->_customerSession->getFormData()) {
        $this->_data = $this->_customerSession->getFormData(); // 获得form data.
        $this->_customerSession->setFormData(false); // 清除session里的form data数据
    }
    return $this->_data;
} 
```

这样在Form页模板文件里就可以使用<?php echo $this->getDataForForm()->get(‘Your key’); ?>了。