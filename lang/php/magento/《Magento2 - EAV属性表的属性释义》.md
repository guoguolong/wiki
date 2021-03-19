Magento所有EAV模型用到的EAV属性都定义在表为eav_attribute中，字段如下：

```
attribute_id : 属性主键ID
entity_type_id : 所属实体类型ID
attribute_code : 属性代码
attribute_model : 属性模型: 暂未使用
backend_model  : 后台模型:按照EAV模型的读取机制进行存储或加载时回调。
backend_type   : 后台类型:如果预定义backend_type使用内置的backend_model，默认就不用说明backend_model了
backend_table  : 后台表：暂未使用
frontend_model : 前端模型:EAV模型读取时前端显示方式。
frontend_input : 前端类型,用于前台显示风格。如果预定义frontend_input使用内置的frontend_model, 默认就不用说明backend_model了
frontend_label : 前端显示标签
frontend_class : 前端显示标签的css类
source_model   : 源模型：显示前端接口时，可能要读取下拉框数据，这里指定数据读取的源
is_required    : 是必须的属性吗？
is_user_defined : 是用户自定义属性吗
default_value  : 默认值
is_unique      : ？ 
note            :  说明
```