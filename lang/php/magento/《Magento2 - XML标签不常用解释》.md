## 1. layout文件中 <update handle="">标签的含义

在view/frontend|adminhtml/layout中的layout文件总是有类似假设有update标签

以adminhtml_newsletter_problem_grid.xml为例：

```xml
<?xml version="1.0"?>
<layout xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <update handle="adminhtml_newsletter_problem_block"/>
    <referenceContainer name="content">
        <block class="Magento\Adminhtml\Block\Newsletter\Problem" name="adminhtml.newsletter.problem.container"/>
    </referenceContainer>
</layout> 
```

看到有个属性handle为adminhtml_newsletter_problem_block的标签update. 当页面加载合并xml时，将寻找各个模块下adminhtml/layout目录中命名为admhtml_newsletter_problem_block.xml的文件，将里面的layout定义与当前layout的定义合并.

## 2. system.xml中的translate=“label"

Magento如下方式调用

```php
$label_value = (string) $node->label;
echo __($label_value); 
```

如果要翻译多个标签，可以 `translate="label type"`