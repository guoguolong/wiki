## Router

```
yarn add @koa/router 
```

## ORM

```
yarn add sequelize
yarn add pg # 支持 postgresql
```

## CORS

自行跨域请求设置中间件注意： OPTIONS 请求要 return，不要执行 next。 当然推荐第三方 cors

```
yarn add @koa/cors
```

## POST请求

```
yarn add koa-bodyparser
```

## validation??

## GraphQL

基础依赖：

```
yarn add @koa/router 
yarn add graphql
```

- [koa-graphql](https://github.com/graphql-community/koa-graphql)

```
yarn add koa-graphql
```

- [apollo server]

```
yarn add apollo-server-koa
```

如果独立使用 apollo-server，直接安装：

```
yarn add apollo-server
```

## GraphQL on Sequalize

```
yarn add graphql-sequelize
yarn add graphql-relay
```