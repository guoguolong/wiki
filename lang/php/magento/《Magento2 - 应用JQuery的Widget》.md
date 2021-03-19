## 1. invoice.js定义了一个jquery的Widget

```php
(function($, window) {
    "use strict";
    $.widget('mage.invoice', {
        /**
         * Options common to all instances of this widget.
         * @type {Object}
         */
        options: {
            deleteConfirmMessage: $.mage.__('Are you sure you want to delete this invoice information?')
        },
        /**
         * Bind event handlers for adding and deleting invoice info.
         * @private
         */
        _create: function() {
            $(this.options.addInvoiceInfo).on('click', $.proxy(this._addInvoiceInfo, this));
            $(this.options.deleteInvoiceInfo).on('click', $.proxy(this._deleteInvoiceInfo, this));
        },
        /**
         * Add a new invoice info.
         * @private
         */
        _addInvoiceInfo: function() {
            window.location = this.options.addInvoiceInfoLocation;
        },
        /**
         * Delete the invoice info whose id is specified in a data attribute after confirmation from the user.
         * @private
         * @param {Event}
         * @return {Boolean}
         */
        _deleteInvoiceInfo: function(e) {
            if (confirm(this.options.deleteConfirmMessage)) {
                if (typeof $(e.target).parent().data('invoice-info') !== 'undefined') {
                    window.location = this.options.deleteUrlPrefix + $(e.target).data('invoice-info');
                }
            }
            return false;
        }
    });
})(jQuery, window); 
```

## 2. Magento页面引用并调用

```php
head.js("<?php echo $this->getViewFileUrl('Haw_Invoice::js/invoice_book.js'); ?>", function() {
        jQuery('table.invoice-info-list').invoice({
            deleteInvoiceInfo: "a[role='delete-invoice-info']",
            deleteUrlPrefix: '<?php echo $this->getDeleteUrl() ?>id/',
            addInvoiceInfo: "button[role=add-invoice-info]",
            addInvoiceInfoLocation: '<?php echo $this->getAddInvoiceInfoUrl() ?>'
        });
    });
```

几点说明：

- 1). head.js是magento的API，就是<script>方式包含指定文件。
-  2). jQuery('table.invoice-info-list’).invoice(..)调用对应widget，注意标签不要匹配多次，否则widget将被调用多次. 注：jQuery(‘…’).invoice需要与invoice.js中 $.widget('mage.invoice', {…中的invoice必须一致
- 3). 要了解jQuery data()方法的使用。 基本地

```javascript
// 向元素附加数据，然后取回该数据：
$("#btn1").click(function(){
  $("div").data("greeting", "Hello World”);
});
$("#btn2").click(function(){
  alert($("div").data("greeting"));
}); 
```

其次,如果id为abc的HTML元素附加了一个属性名字为: data-invoice-info，那么jQuery(‘#abc’).data(‘invoice-info’)就可以取得该属性的值。

- 4). jQuery('div.page.main').invoice(…)自动回调该widget的_create方法，该方法通常实现一些事件绑定。