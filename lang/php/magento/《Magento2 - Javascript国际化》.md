Magento2中JavaScript国际化功能由translate.js文件实现。假设info.js有些字符串要国际化，方法如下：

## 1. 加入国际化字符串翻译

```javascript
// 假设提供当前页面locale变量
if(locale == 'zh_CN’) {
  jQuery.mage.translate.add('Are you sure you want to delete this item?', ‘您确认要删除这个项目'); 
}
if(locale == ‘en_US’) {
  jQuery.mage.translate.add('Are you sure you want to delete this item?', ‘Did you really want to remove it?'); 
}
```

## 2. 使用国际化字符串翻译

```javascript
jQuery.mage.__('Are you sure you want to delete this invoice information?’);
```

当然，实际应用场景jQuery.mage.translate.add大多放在phtml文件中，这样就可以统一使用Magento后台csv文件的翻译字符串了。上述例子可以修改为：

```html
<script type="text/javascript">
    (function($) {
        $.mage.translate.add('Are you sure you want to delete this invoice information?', '<?php echo __('Are you sure you want to delete this invoice information?');?>');
    })(jQuery); 
```

如果你想添加一组翻译字符串，还可以用add一个Json对象

```php
<?php
$_data = array(
    'Please select an option.' => __('Please select an option.'),
    'This is a required field.' => __('This is a required field.’),
);
<script type="text/javascript">
    (function($) {
        $.mage.translate.add(<?php echo Zend_Json::encode($_data) ?>)
    })(jQuery);
</script> 
```