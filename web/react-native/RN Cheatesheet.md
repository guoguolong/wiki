# React Native CheatSheet



## Styles

**不支持列表**

|CSS描述|不支持的值|支持的值|替代方案|
|---|---|---|---|
|border|none|||
|伪类选择器|ALL||:active用hoverStyle属性|
|组合选择器|ALL|||
|通配选择器|ALL|||
|display|inline, inline-block, block|||
|background|ALL||用 background-color 替代|
|box-sizing|ALL|||
|animation|ALL|||
|animation-delay|ALL|||
|outline|ALL|||
|transform-origin|ALL|||
|vertical-align|ALL|||

* 样式 class 在代码在标签里的定义顺序很重要，后者覆盖前者的样式。不取决于 scss/css的定义顺序。

* `<View>`不支持 style 的 color 属性， 只有 `<Text>`支持 style 的 color属性。

* JS中不能写复合 css 属性，如padding，应分开写paddigTop,paddingBottom...

* display: inline、border: none会导致Expo Go挂

* Expo Go闪退

  ```
  1. display: inline、border: none
  2. 一个错误的CSS属性值会导致崩溃。可能是不存在的字符串，或者类型错误（如错把width的值0写成'')
  ```

* 在 JS 中，RN组件样式不支持复合CSS属性，如border不可以，应单独写borderWidth等

* stylelint提示不支持，实际测试可以支持的CSS属性

  ```
  border-left
  border-bottom
  ```
  

  


参考：

* https://github.com/vhpoet/react-native-styling-cheat-sheet
* https://www.codecademy.com/learn/learn-react-native/modules/core-components-react-native/cheatsheet