# 一般cookie的用法

一段典型的设置cookie到浏览器的方法

```js
res.cookie('loginname', 'allen', {
  maxAge: 600000,
  httpOnly: true,
  path: '/',
  // secure:true //设置该选项，只有https网站才可以输出该cookie
});
```

那么下次访问同一个网站下一个页面时，该cookie就被浏览器带回，通过下面方式获取cookie

```js
console.log(req.header.cookie);
```

# 加密cookie的用法

```
res.cookie('loginname', 'allen', {

    signed: true,

    maxAge: 600000,

    httpOnly: true,

    path: '/',

});
```

如果cookie设置时，增加signed字段为true，它就会寻找req.secret(一个秘钥字符串)，进行加密 allen返回浏览器，如果你没有设置过该字段，那么express会提示：

> cookieParser("secret") required for signed cookies

此时cookie-parser上场了，在res.cookie设置中间件：

```js
const cookieParser = require('cookie-parser');
// app 是express的实例
app.use(cookieParser('lifeissimpebutyoumadeitcomplicated'));
```

间接设置了上述密钥到 req.secret。 这是再执行res.cookie，输出到浏览器的cookie形如：

> loginname: "s:allen.iqdsqdhEgTS8bbT+0gMEnz2ZlG6GasNpNME2F/5vCZs" path: "/" expires: "2017-03-03T02:56:41.000Z" httpOnly: true

那么下次访问同一个网站下一个页面时，该cookie就被浏览器带回，cookie-parser注入了两个新的方法：

1. req.cookie # 用于获取未加密的cookie(即：使用cookieParser的参数设置为空)
2. req.signedCookie # 用于获取加密的cookie(即：使用cookieParser的参数设置为空) 所以这里通过req.singedCookies获取loginname解秘后的值（req.cookies里就取不到了）。当然如果你想拿到浏览器传过来的cookie原始数据，总是可以通过express的方法获得：

```js
console.log(req.headers.cookie);
```

# express-session和加密cookie的关系

通常session也是使用一个特殊的cookie来标识用户ID，比如：你使用express-session，那么cookie就会被设置一个名字为connect.sid的cookie，形如：

> connect.sid: s:_EbRSyS4Ean8-IfeKGCJ1pEhrZmYAW7i.xOXbx6ACB6Ts/kGLB0WGEssXkheNCsCJ0Dtsb4LbsR4

该module依赖cookie-parser：在express-session加载之前先加载 cookie-parse(你可以不加载cookie-parser,不会报错，但是也不工作，哈哈)

```js
let secret = 'lifeissimpebutyoumadeitcomplicated';

app.use(require('cookie-parser')(secret)); // app 是express的实例
app.use(require('express-session')({
  resave: false,
  saveUninitialized: true,
  secret: secret
}));
```

这样当访问启动了express-ssession网站的时候，如果发现req没有传递connect.sid，那么res.cookie设置一个加密的名字为connect.sid的cookie，使用的密钥就是lifeissimpebutyoumadeitcomplicated；

当访问下一个页面（当前网站）时，发现req传递回来了connect.sid，它就尝试用前面 express-session设置里面的secret来解密connect.sid到req.signedCookie；

所以 1 如果cookie-parser和express-session使用秘钥不同，解密的req.signedCookies['connect.sid'] = false； 2 如果cookie-parser没有设置秘钥，那req.signedCookies['connect.sid'] = '浏览器传过来的值'，session至少还可以工作； 3 如果两个秘钥相同，正常工作，req.signedCookies['connect.sid'] 返回类似这样的值：DYjKZmAahpE1oM5zxzReiCL437hOyFlJ