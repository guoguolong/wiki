## graphql-request的经验

## Apollo Client 的 C 之后，列表没有刷新.

要么设置 fetchPolicy: network-only或no-cache，要么C之后编辑 update 回调函数，自行更新 cache 列表

## 单独的 .gql 的加载

### graphql-tag官方方案

https://github.com/apollographql/graphql-tag

```
{
  ...
  loaders: [
    {
      test: /\.(graphql|gql)$/,
      exclude: /node_modules/,
      loader: 'graphql-tag/loader'
    }
  ],
  ...
}
```

安装 react-app-rewire-graphql-tag，使上述配置简化(见官网介绍)

```
yarn add -D react-app-rewire-graphql-tag
```

然后代码中就可以直接使用类似如下代码：

```
import { MyQuery1, MyQuery2 } from 'query.graphql'
```

> 缺点：不支持 TypeScript 中直接导入 .gql

### 使用 graphql.macro

[create-react-app 官方推荐方案](https://create-react-app.dev/docs/loading-graphql-files/)

```
yarn add graphql.macro
```

然后代码中就可以直接使用类似如下代码：

```
import { loader } from 'graphql.macro';
const query = loader('./foo.graphql');
```

> 注：因为 graphql.macro 加载多个定义的时候，没有方便的 API 检索里面的子节点，所以通常一个query/mutation定义一个 .gql文件

### 在 Node 中加载 .gql 文件

```
yarn add graphql-import
const { importSchema } = require('graphql-import')
const typeDefs = importSchema(__dirname + '/fool.gql'); // 不要用当前路径. 有bug
```

因为 graphql-import 已经弃用，请参考： [graphql-tools](https://www.graphql-tools.com/docs/migration-from-import/)

### TypeScript中直接导入 .gql

前面 graphql-tag 官方 import 方法不支持 typescript。以下俩方法均测试失败：

- graphql-tag 推荐的预处理 [ts-transform-graphql-tag](https://github.com/firede/ts-transform-graphql-tag)
- 一篇参考：[How to resolve import for the .graphql file with typescript and webpack](https://dev.to/open-graphql/how-to-resolve-import-for-the-graphql-file-with-typescript-and-webpack-35lf)

最后我选择了 graphql.macro 回避直接 import 的机制。