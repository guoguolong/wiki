# Vitejs和pnpm link调试库项目

有库项目 @guoguolong/react-ui-nation-select (https://github.com/guoguolong/fe-tools-tutorial.git)

* 使用 rollup 构建，传统方式构建（单体文件）到dist目录
* `pnpm dev:rollup` 监控代码变化生成到 dist目录

现在开发组件过程中希望，修改组件代码就触发调试页面看到变化，一种方法是使用 `pnmp link`(https://pnpm.io/cli/link)，步骤如下：

假设 `@guoguolong/react-ui-nation-select` 位于 `/Users/koda/libs/react-ui-nation-select`

## 组件项目侧

* 命令行运行

```bash
pnpm dev:rollup
```



## 页面应用侧

* 找到一个现有的页面应用（vitejs组织的项目），修改 `vite.config.ts`

  ```javascript
  export default defineConfig({
    server: {
      port: 3000,
      fs: {
        allow: [
          "src", // 默认值
          "/Users/koda/libs/react-ui-nation-select"
        ]
      },
    }
    //....
  });
  ```

* 命令行下执行

  ```bash
  pnpm link /Users/koda/libs/react-ui-nation-select
  ```

* 源码中引用  `@guoguolong/react-ui-nation-select`

  ```jsx
  import NationSelect from '@guoguolong/react-ui-nation-select'
  
  export default ()=>{
    return <NationSelect bgColor="lightgreen" />
  }
  ```

接下来，每次修改 组件项目侧代码，应用侧浏览器页面就会自动刷新了