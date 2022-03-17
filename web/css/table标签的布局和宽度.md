## table-layout = auto

设置总宽W：

* 如果设置列宽度 C1...Cn，**渲染表格宽度是W**，按**列宽设置**值同比显示列。
* 如果没有设置列宽，尽可能以接近W的宽度，按**内容**同比例显示列，即：如果每列最小宽之和超过W，**那就忽略W**，按照列宽之和显示表格，**否则显示为W宽**。 (参考例子：https://developer.mozilla.org/en-US/docs/Web/CSS/table-layout#fixed-width_tables_with_text-overflow)



## table-layout = fixed

设置总宽W：

* 设置列宽度 C1...Cn，
  * 如果 (C1 + ... Cn) > W，**忽略渲染表格宽度W**，按列宽设置实际像素宽显示列；
  * 如果 (C1 + ... Cn) <= W，**渲染表格宽度是W**，按列宽设置值同比显示列
* 如果没有设置列宽，**渲染表格宽度是W**，每列宽度一样。

