## 1. JQuery Widget定义

```javascript
(function ($, window) {
    $.widget(widgetName1',  {
         _create: function () {
            // 实例化该widget时，自动调用该回调函数，加入各种监听方法.
        },
         _otherMethod: function (param) {
           // 各种逻辑
        }
      });
}) (jQuery, window);   
```

## 2. JQuery Widget调用

```javascript
jQuery(’tag name|class|id').widgetName1(); 
// 将自动调用widget的_create方法
```

## 3. 带传入参数的Widget定义

```javascript
(function ($, window) {
    $.widget(widgetName1',  {
        options: {
            ‘param1’: null,
            ‘param2’: null,
            ‘param3’: null
          },         
         _create: function () {
            // 实例化该widget时，自动调用该回调函数，加入各种监听方法.
        },
         _otherMethod: function (param) {
           // 各种逻辑
        }
      });
}) (jQuery, window);   
```

## 4. 调用Widget并传入参数：

```javascript

jQuery('tag name|class|id').widgetName1({
   param1: ‘abc’,
   param2: ‘def’,
   param3: ‘ghi’
}); 
```

## 5. Widget继承

```javascript
(function ($, window) {
    $.widget(widgetName2’,  $widgetName1, {
         _create: function () {
            // 实例化该widget时，自动调用该回调函数，加入各种监听方法.
        },
         _otherMethod: function (param) {
           this._super(param) ; // 调用父类方法
            // 各种逻辑
        },
         _method3: function () {
           // 各种逻辑
        },
         _method4: function () {
           // 各种逻辑
        }
    });
}) (jQuery, window);   

// 定义了widgetName2继承widgetName1，新增了更多逻辑方法：_method3、_method4
```

JQuery Widget更多，请参考：http://learn.jquery.com/jquery-ui/widget-factory/extending-widgets/