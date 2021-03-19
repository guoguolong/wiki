## Form表单中enctype属性的三种类型

form表单中enctype属性可以用来控制对表单数据的发送前的如何进行编码，enctype有三种，分别为：

### 1. application/x-www-form-urlencoded

在发送前会编码所有字符，即在发送到服务器之前，所有字符都会进行编码（空格转换为 "+" 加号，"+"加号转换为空格，特殊符号转换为 ASCII HEX 值）。

表单输入：FirstName=Mickey， LastName=Mouse，并设置 application/x-www-form-urlencoded时，

#### Header：

```
content-type:application/x-www-form-urlencoded
```

#### Body：

```
FirstName=Mickey&LastName=Mouse
```

### 2. multipart/form-data

不对字符编码，可以用于发送二进制的文件（其他两种类型不能用于发送文件）。

表单输入：FirstName=Mickey， LastName=Mouse，并设置 multipart/form-data 时，

#### Header：

```
content-type:multipart/form-data; boundary=----WebKitFormBoundaryR6Bl1kfcrgv43HIQ
```

#### Body：

```
------WebKitFormBoundaryR6Bl1kfcrgv43HIQ
Content-Disposition: form-data; name="FirstName"

Mickey
------WebKitFormBoundaryR6Bl1kfcrgv43HIQ
Content-Disposition: form-data; name="LastName"

Mouse
------WebKitFormBoundaryR6Bl1kfcrgv43HIQ--
```

### 3. text/plain

用于发送纯文本内容，空格转换为 "+" 加号，不对特殊字符进行编码，一般用于email之类的；

**其中application/x-www-form-urlencoded为默认类型。**

另外，让服务器按照json、二进制等方式读取浏览器传递的数据，都是通过ajax，明确指定 content-type 为 application/json、application/octet-stream 来实现的。

注：对于 postman 的 post方式，选择binary方式，并不能正确设置content-type: application/octet-stream，