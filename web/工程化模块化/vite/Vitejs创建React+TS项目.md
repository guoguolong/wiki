[toc]
# Vitejs创建React+TS项目

现在已经到了 vitejs 创建 react脚手架的项目的时代了（2023年1月） https://vitejs.dev/

运行下面命令， 就能生成干净脚手架项目了

```bash
pnpm create vite my-vue-app --template react-ts
```

## require如何引用 ES格式的库（鸡肋）

1. 使用 require插件 vite-plugin-require，配置如下

   ```javascript
   import { defineConfig } from 'vite'
   import react from '@vitejs/plugin-react'
   import vitePluginRequire from "vite-plugin-require";
   
   // https://vitejs.dev/config/
   export default defineConfig({
     plugins: [react(), vitePluginRequire.default({})],
   })
   ```

   **小知识：**

   > 如果最近的package.json文件包含一个顶级字段`"type":"module"`，则以 **.js 结尾**或**没有任何扩展名**的文件将作为ES模块进行加载。
   >
   > 如果最近的package.json**缺少"type"字段**，或者包含`"type":"commonjs"`，则**无扩展名的文件**和**.js结尾**文件将被视为commonjs。
   >
   > 如果一直到卷根，还是没找到package.json, Node.js则按默认规则运行，就像package.json中没有"type"字段。"无扩展"指的是不包含扩展名的文件路径，而不是在说明符中选择性地删除文件扩展名。

   vitejs 脚手架项目根目录下的 package.json 默认设置为 `"type":"module"`，但是`vite-plugin-require`是 cjs格式，所以用 import导入时要加`.default`，如果想去掉`default`，package.json中设置为：`"type":"commonjs"`或删除。

2. 要求**ES**库必须有default导出（`export const xxx`这种不能用 ）
   比如ES库 abc 导出如下：

   ```javascript
   export default {
     Button: 'a',
     TextArea: 'b',
   }
   ```

   在代码中引用

   ```javascript
   const abc, { Button } require('abc')
   ```

   好鸡肋：即然是 ES 库，那就直接用 import多好，毕竟，哪个前端应用不支持 import呢？

## require引用CJS格式的库
使用 vitejs 插件`vite-plugin-commonjs`

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import commonjs from 'vite-plugin-commonjs'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    react(), 
    commonjs(),
  ],
})
```

然后，可以试试  `require('antd-mobile')`，会发现同样可以工作。值得注意的是：`vite-plugin-commonjs`貌似还不够完善，对于umd包格式文件，require会报错（可以用 antd-mobile.umd.js 来测试），但是如果在 webpack项目里require（webpack支持require）umd包格式文件则可以。

## less： 默认支持

postcss + auroprefixer 配置？

   