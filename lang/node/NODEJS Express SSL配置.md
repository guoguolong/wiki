## 创建私有证书

使用阮一峰推荐的命令行一键生成工具....生成俩文件：

```
1. 私钥： private.pem
2. 公钥：file.cert
```

## express配置

```js
var https = require('https');
var privateKey = fs.readFileSync(path.join(__dirname, './certificate/private.pem'), 'utf8');
var certificate = fs.readFileSync(path.join(__dirname, './certificate/file.crt'), 'utf8');
var credentials = {key: privateKey, cert: certificate};
var httpsServer = https.createServer(credentials, app);
httpsServer.listen(SSLPORT, function() {
    console.log('HTTPS Server is running on: https://localhost:%s', SSLPORT);
});
```