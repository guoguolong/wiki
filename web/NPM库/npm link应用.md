# npm link的应用

## 场景

在本地开发 npm 模块的时候，可以npm link命令，将模块链接到对应的运行项目中去，方便地对模块进行调试和测试

## 使用方法

有两个项目：
1. `hero-ui` 组件库项目，即我们要开发的模块；
2. `hero-app` 一个是要调用模块的应用项目

### 1. 在 hero-ui 模块项目中创建链接
```bash
cd hero-ui
npm link
```
执行命令后，hero-ui 会根据 package.json上 的配置，被链接到全局 node_modules 目录，一般路径是`{prefix}/lib/node_modules/<package>`，这是官方文档上的描述，我们可以使用`npm config get prefix`命令获取到prefix的值

```bash
npm config get prefix
```
在macOS下，一般返回值是： '/usr/local'，那么该包 link 后的值就是 `/usr/local/lib/node_modules/hero-ui`

### 2. 在 hero-app 项目中引用链接

```bash
cd hero-app
npm link hero-ui`
```

### 3. 在 hero-app 项目使用 hero-ui 模块

```jsx
import heroUI from 'hero-ui';
console.log("heroUI", heroUI);
```