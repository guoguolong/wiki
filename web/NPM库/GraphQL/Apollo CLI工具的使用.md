使用Apollo时，CLI工具利器，它可以：

1. service:push - 发布 Schema 到 https://engine.apollographql.com，在团队内分享
2. client:codegen - 根据 Schema 产生 TypeScript 的接口定义

## 安装 Apollo Cli

```
npm i -g apollo
```

## 发布Schema： service:push

假设基于标准 GraphQL 的Web服务器成功启动（如启动在本机 4000 端口）；然后进入https://engine.apollographql.com，创建一个 bucket 名字为 magic-mooc，那么就可以用如下命令

```
 apollo service:push --graph=magic-mooc --key=user:gh.guoguolong:vKOUWxxp71eyns94RnorPg --endpoint=http://localhost:4000 
```

即可发布 Schema 到 [engine.apollographql.com](http://engine.apollographql.com)，分享给团体其他成员使用

可以将 apollo 命令行参数配置到 .env 和 apollo.config.js，简单使用 `apollo service:push`

**# apollo.config.js**

```
module.exports = {
    service: {
        name: 'magic-mooc',
        endpoint: {
            url: 'http://localhost:4000/graphql'
        }
    }
}
```

**# .env**

```
APOLLO_KEY=user:gh.guoguolong:vKOUWxxp71eyns94RnorPg
```

再试试执行 `aplollo service:push`

## 产生 TypeScript 接口：client:codegen

React客户端常用TypeScript开发，`apollo client:codegen` 可以根据 GraphQL Schema定义，并扫描 src 目录（包括子目录）下的 gql 定义，生成 TypeScript 接口在相应的 **generated** 子目录，以备后续使用。

不幸，我只能通过 schema.graphql 文件方式测试成功，示例如下：

### 步骤 1.创建 apollo.config.js

进入项目根目录（已经写好的src子目录包含 很多 gql定义），创建 apollo.config.js

```
module.exports = {
    client: {
        service: {
            localSchemaFile: "./schema.gql"
        }
    }
}
```

### 步骤 2. 下载 schema.gql

访问服务器Graph Endpoint: http://localhost:4000，下载 schema 文件另存为 schema.gql

### 步骤 3. 产生 TypeScript 接口文件

```
apollo client:codegen --target typescript --watch
```

> 参考：[Configuring Apollo projects](https://www.apollographql.com/docs/devtools/apollo-config)