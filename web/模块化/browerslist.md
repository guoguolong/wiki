# Browserslist

https://github.com/browserslist/browserslist

`Browserslist` 可以设置指定目标浏览器列表，供相关工具针对指定的浏览器列表进行合适的操作。最典型的工具就是 [Autoprefixer](https://github.com/postcss/autoprefixer)，其可以将源 css 文件转换为 `Browserslist` 配置指定的目标浏览器的目标 css 文件，让 css 在这些目标浏览器里尽可能地运行。

## 安装 browserslist

```
npm i browserslist
```

## 运行 browserslist

### 1. 获得默认目标浏览器配置

```
npx browserslist
```
返回目标浏览器列表：
```
and_chr 89
and_ff 86
and_qq 10.4
and_uc 12.12
android 89
baidu 7.12
chrome 89
chrome 88
chrome 87
edge 89
edge 88
firefox 87
firefox 86
firefox 78
ie 11
ios_saf 14.0-14.5
ios_saf 13.4-13.7
kaios 2.5
op_mini all
op_mob 62
opera 73
opera 72
safari 14
safari 13.1
samsung 13.0
samsung 12.0
```
以上默认配置实际是默认值：
```
0.5%, last 2 versions, Firefox ESR, not dead
```
也就是说，运行
```
npx browserslist '> 0.5%, last 2 versions, Firefox ESR, not dead'
```
将会得到同样的结果。

>npx 小贴士：`npx browserslist` 可以直接执行，不必一定先执行 `npm install browserslist`，那么它将即时下载 npm 仓库里 `browserslist`  包并运行，所以需要联网进行

### 2. 获得指定语法（非默认）产生的目标浏览器配置

```
npx browserslist '<Query语法>'
```

如，全球超过 1%的人使用的浏览器（> 1%）的配置结果是什么呢？运行：
```
npx browserslist '> 1%'
```
返回结果如下：
```
and_chr 89
and_uc 12.12
chrome 89
chrome 88
edge 89
firefox 86
ios_saf 14.0-14.5
safari 14
samsung 13.0
```

`目标浏览器 Queries 语法部分列表`

| 语法                       | 说明                                                       |
| -------------------------- | ---------------------------------------------------------- |
| > 1%                       | 全球超过 1%的人使用的浏览器                                |
| > 5% in US                 | 美国超过 5%的人使用的浏览器                                |
| last 2 versions            | 所有浏览器兼容到最后两个版本（根据 caniuse.com追踪的版本） |
| Firefox ESR                | Firefox最新版本                                            |
| Firefox > 20               | 执行 超过 20版本的Firefox                                  |
| not ie < 8                 | ie不小于8的浏览器                                          |
| unreleased versions        | 所有浏览器的测试版本                                       |
| unreleased chrome versions | Chrome浏览器的测试版本                                     |
| ie8                        | 指定 IE8 版本，注意：not 语法前面必须搭配一个非not 语法                                              |
注： Queries 语法不区分大小写



## Queries 配置文件

可以把 Queryies 配置写到配置文件（package.json、.browserslistrc等）里，推荐写在 package.json 的 `browserslist`字段，如：
```json
{
  "private": true,
  "dependencies": {
    "autoprefixer": "^6.5.4"
  },
  "browserslist": [
    "last 1 version",
    "> 1%",
    "IE 10"
  ]
}
```
这样，执行 `npx browserslist` 将把配置文件里的配置作为默认的query产生目标浏览器配置。

### 为不同环境配置
在 package.json 中，可以说明 BROWSERSLIST_ENV 或 NODE_ENV 进行访问不同的环境节点下的配置，如果没有设置上述变量，将默认访问 production 节点下的配置。
```json
  "browserslist": {
    "production": [
      "> 1%",
      "ie 10"
    ],
    "modern": [
      "last 1 chrome version",
      "last 1 firefox version"
    ],
    "ssr": [
      "node 12"
    ]
  }
```

更多详细，请参考官网 README。了解了 browserslist 后，一定要使用工具，如 Autoprefixer 进行实践一下，看看 browserslist 如果实现为 css 增加合适的厂商前缀的。

