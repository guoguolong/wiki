PHP error_log设置

1. 如果想显示所有级别错误，请使用

   ```
   error_reporting = E_ALL
   ```

2. 如果想关闭error在页面上显示，设置：

   ```
   display_errors = Off
     
   如果想显示到log文件中，进一步设置：
   error_log = /tmp/php_errors.log
   ```