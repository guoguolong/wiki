## 步骤1. 重写Onepage控制器类，主要是为了新增的步骤添加”继续”按钮点击后的服务器行为

```php
<?php
namespace Jam\Invoice\Controller;
class Onepage extends \Magento\Checkout\Controller\Onepage {
    /**
     * Save payment ajax action
     *
     * Sets either redirect or a JSON response
     * 由于发票增加到Payment步骤之后，所以要重定义该方法，指定合适的section.
     */
    public function savePaymentAction() {
        if ($this->_expireAjax()) {
            return;
        }
        try {
            if (!$this->getRequest()->isPost()) {
                $this->_ajaxRedirectResponse();
                return;
            }
            $data = $this->getRequest()->getPost('payment', array());
            $result = $this->getOnepage()->savePayment($data);
            // get section and redirect data
            $redirectUrl = $this->getOnepage()->getQuote()->getPayment()->getCheckoutRedirectUrl();
            if (empty($result['error']) && !$redirectUrl) {
                $result['goto_section'] = 'invoice';
                $result['update_section'] = array(
                    'name' => 'invoice',
                    'html' => $this->_getInvoiceHtml() // _getInvoiceHtml()不是必须的，如果你不需要在客户端网页挂接conteUpdate的js方法
                );
            }
            if ($redirectUrl) {
                $result['redirect'] = $redirectUrl;
            }
        } catch (\Magento\Payment\Exception $e) {
            if ($e->getFields()) {
                $result['fields'] = $e->getFields();
            }
            $result['error'] = $e->getMessage();
        } catch (\Magento\Core\Exception $e) {
            $result['error'] = $e->getMessage();
        } catch (\Exception $e) {
            $this->_objectManager->get('Magento\Logger')->logException($e);
            $result['error'] = __('Unable to set Payment Method');
        }
        $this->getResponse()->setBody($this->_objectManager->get('Magento\Core\Helper\Data')->jsonEncode($result));
    }
    // 发票SECTION点击继续按钮时候将执行 
    public function saveInvoiceAction() {
        if ($this->_expireAjax()) { return; }
        try {
            if (!$this->getRequest()->isPost()) {
                $this->_ajaxRedirectResponse();
                return;
            }
            $data = $this->getRequest()->getPost('invoice', array());
            // $result = $this->getOnepage()->savePayment($data);
            // get section and redirect data
            $redirectUrl = $this->getOnepage()->getQuote()->getPayment()->getCheckoutRedirectUrl();
            if (empty($result['error']) && !$redirectUrl) {
                $result['goto_section'] = 'review';
                $result['update_section'] = array(
                    'name' => 'review',
                    'html' => $this->_getReviewHtml()
                );
            }
            if ($redirectUrl) {
                $result['redirect'] = $redirectUrl;
            }
        } catch (\Magento\Payment\Exception $e) {
            if ($e->getFields()) {
                $result['fields'] = $e->getFields();
            }
            $result['error'] = $e->getMessage();
        } catch (\Magento\Core\Exception $e) {
            $result['error'] = $e->getMessage();
        } catch (\Exception $e) {
            $this->_objectManager->get('Magento\Logger')->logException($e);
            $result['error'] = __('Unable to set Payment Method');
        }
        $this->getResponse()->setBody($this->_objectManager->get('Magento\Core\Helper\Data')->jsonEncode($result));
    }
    // 如果你不需要在客户端网页挂接conteUpdate的js方法
    public function invoiceAction() {
        if ($this->_expireAjax()) {
            return;
        }
        $this->addPageLayoutHandles();
        $this->loadLayout(false);
        $this->renderLayout();
    }
    protected function _getInvoiceHtml() {
        return $this->_getHtmlByHandle('jaminvoice_onepage_invoice');
    }
} 
```

## 步骤2. 定制的onepage.phtml增加一个AJAX提交的url钩子

```
<?php
$_paymentBlock = $this->getLayout()->getBlock('checkout.onepage.payment');
$_registerParam = $this->getRequest()->getParam('register');
?>
<div class="opc wrapper">
    <ol class="opc" id="checkoutSteps">
    <?php $i=0; foreach($this->getSteps() as $_stepId => $_stepInfo): ?>
    <?php if (!$this->getChildBlock($_stepId) || !$this->getChildBlock($_stepId)->isShow()): continue; endif; $i++ ?>
        <li id="opc-<?php echo $_stepId ?>" class="section<?php echo !empty($_stepInfo['allow'])?' allow':'' ?><?php echo !empty($_stepInfo['complete'])?' saved':'' ?>">
            <div class="step-title">
                <span class="number"><?php echo $i ?></span>
                <h2><?php echo $_stepInfo['label'] ?></h2>
                <!--<a href="#"><?php echo __('Edit') ?></a>-->
            </div>
            <div id="checkout-step-<?php echo $_stepId ?>" class="step a-item" style="display:none;">
                <?php echo $this->getChildHtml($_stepId) ?>
            </div>
        </li>
    <?php endforeach ?>
    </ol>
    <script type="text/javascript">
        (function($) {
            head.js(
                "<?php echo $this->getViewFileUrl('jquery/jquery.validate.js')?>",
                "<?php echo $this->getViewFileUrl('jquery/jquery.metadata.js')?>",
                "<?php echo $this->getViewFileUrl('mage/validation.js')?>",
                "<?php echo $this->getViewFileUrl('mage/validation/validation.js')?>",
                "<?php echo $this->getViewFileUrl('Magento_Checkout::js/accordion.js') ?>",
                function() {
                    $('#checkoutSteps')
                        .accordion({
                            activeSelector: '#opc-<?php echo $this->getActiveStep() ?>'
                        })
                        .opcheckout({
                            quoteBaseGrandTotal: <?php echo (float)$_paymentBlock->getQuoteBaseGrandTotal() ?>,
                            progressUrl: '<?php echo $this->getUrl('checkout/onepage/progress') ?>',
                            reviewUrl: '<?php echo $this->getUrl('checkout/onepage/review') ?>',
                            failureUrl: '<?php echo $this->getUrl('checkout/cart') ?>',
                            getAddressUrl: '<?php echo $this->getUrl('checkout/onepage/getAddress') ?>address/',
                            checkoutAgreements: '#checkout-agreements',
                            checkoutProgressContainer: '#checkout-progress-wrapper',
                            checkout: {
                                suggestRegistration: <?php echo ($_registerParam || $_registerParam === '') ? 'true' : 'false' ?>,
                                saveUrl: '<?php echo $this->getUrl('checkout/onepage/saveMethod') ?>'
                            },
                            billing: {
                                saveUrl: '<?php echo $this->getUrl('checkout/onepage/saveBilling') ?>'
                            },
                            shipping: {
                                saveUrl: '<?php echo $this->getUrl('checkout/onepage/saveShipping') ?>'
                            },
                            shippingMethod: {
                                saveUrl: "<?php echo $this->getUrl('checkout/onepage/saveShippingMethod') ?>"
                            },
                            payment: {
                                <?php if ($_paymentBlock->getChildBlock('methods')->getSelectedMethodCode()): ?>
                                    defaultPaymentMethod: "<?php echo $_paymentBlock->getChildBlock('methods')->getSelectedMethodCode() ?>",
                                <?php endif ?>
                                saveUrl: '<?php echo $this->getUrl('checkout/onepage/savePayment') ?>'
                            },
                            invoice: {
                                saveUrl: '<?php echo $this->getUrl('jaminvoice/onepage/saveInvoice') ?>'
                            },
                            review: {
                                saveUrl: '<?php echo $this->getUrl('checkout/onepage/saveOrder') ?>',
                                successUrl: '<?php echo $this->getUrl('checkout/onepage/success') ?>'
                            },
                            methodDescription : '.items'
                        });
                });
        })(jQuery);
    </script>
</div>
```

## 步骤3. 定制的invoice.phtml，使其有效

```html
<form action="" class="form" id="co-invoice-form" data-hasrequired="<?php echo __('* Required Fields') ?>">
    <fieldset class="fieldset" >
        <div class="field required">
            <label class="label" for="invoice:title"><span><?php echo __('Invoice Title') ?></span></label>
            <div class="control">
                <input type="text" title="<?php echo __('Invoice Title') ?>" name="invoice[title]" value="" class="input-text required-entry valid" id="invoice:title" />
            </div>
        </div>
    </fieldset>
    <div class="actions buttons-set form-buttons" id="invoice-buttons-container">
    <div class="primary"><button type="button" class="button action continue"><span><?php echo __('Continue') ?></span></button></div>
        <div class="secondary"><a href="#" class="action back"><span><?php echo __('Back') ?></span></a>
        <span id="invoice-please-wait" class="please-wait load indicator" style="display:none;"><span><?php echo __('Loading next step...') ?></span></span>
    </div>   
</form>
<script>
(function($, window) {
    'use strict';
    $.widget('mage.opcheckout', $.mage.opcheckout, {
        options: {
            invoice: {
                form: '#co-invoice-form',
                continueSelector:'#invoice-buttons-container .button'
            }
        },
        _create: function() {
            this._super();
            this.element
                .on('click', this.options.invoice.continueSelector, $.proxy(function() {
                    if ($(this.options.invoice.form).validation && $(this.options.invoice.form).validation('isValid')) {
                        this._ajaxContinue(this.options.invoice.saveUrl, $(this.options.invoice.form).serialize());
                    }
                }, this))
                .find(this.options.invoice.form).validation();
        }
    });
})(jQuery, window);
</script>
```