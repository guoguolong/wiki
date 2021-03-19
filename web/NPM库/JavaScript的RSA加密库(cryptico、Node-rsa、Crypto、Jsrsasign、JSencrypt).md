先了解下openssl：利用openssl生成公钥私钥

- 生成公钥：

```
openssl genrsa -out rsa_private_key.pem 1024
```

生成私钥:

```
openssl rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
```

下面介绍 JavaScript 库

## 一、Cryptico

```
npm i cryptico
```

- 优点：
  rsa密钥生产、rsa加密、rsa解密，使用方式简单
- 缺点：
  生产的密钥不是标准位数的rsa密钥；不支持标准位数的rsa密钥pem格式；七年前已经停止更新；加密中文会乱码；

```
前端和nodejs服务端都可以使用
```

## 二、node-rsa

```
npm i node-rsa
```

- 优点：
  可以生产不同填充方式的rsa公钥和私钥，并且生产密钥可以在nodejs中的crypto加密解密使用；
- 缺点：
  crypto加密可以使用node-rsa解密，但是node-rsa加密的不可以使用crypto模块解密(更换解密私钥的填充方式应该可以)；生产密钥时间较长；

```
前端和nodejs服务端都可以使用
```

## 三、Crypto

crypto模块的目的是为了提供通用的加密和哈希算法。用纯JavaScript代码实现这些功能不是不可能，但速度会非常慢。Nodejs用C/C++实现这些算法后，通过cypto这个模块暴露为JavaScript接口，这样用起来方便，运行速度也快。 NodeJS的模块crypto，有以下4个与公钥加密相关的类。

- ①　Cipher: 用于加密数据；
- ②　Decipher: 用于解密数据；
- ③　Sign: 用于生成签名；
- ④　Verify: 用于验证签名；
- 
- 优点：
  生产密钥速度快，耗时是cryptcio和node-rsa的十分之一；nodejs内置模块；
- 缺点：
  公钥不支持pcks8，前端和其对应的库较少；

`仅 nodejs 服务端可以使用。`只可以在nodejs端使用（从后面的例子可以看到，更改密钥的填充方式，可以和前端部分rsa加密方式通用，估计这也就是为何node加密模块crypto用的少的原因）

### DEMO

1. nodejs支持的加密算法和哈希算法有哪些？

```js
const crypto = require('crypto')
// 查看含有哪些加密算法
var Ciphers=crypto.getCiphers()
console.log(Ciphers)
// (171) ['aes-128-cbc', 'aes-128-ccm', 'aes-128-cfb',...]可以看到共有171种加密算法（没有rsa）

// 查看含有哪些哈希算法
var Hashes = crypto.getHashes()
console.log(Hashes);
// (59) ['RSA-MD4', 'RSA-MD5', 'RSA-MDC2', 'RSA-RIPEMD160', 'RSA-SHA1',...]可以看到共有59种哈希算法
```

1. 生产rsa公钥私钥（pkcs1的公钥publicKey长度为188）

```js
const { generateKeyPairSync } = require('crypto');
const { publicKey, privateKey } = generateKeyPairSync('rsa', {
  modulusLength: 1024,
  publicKeyEncoding: {
    type: 'pkcs1',
    format: 'pem'
  },
  privateKeyEncoding: {
    type: 'pkcs8',
    format: 'pem',
    cipher: 'aes-256-cbc',
    passphrase: 'top secret'
  }
});
```

1. 公钥加密，私钥解密；私钥加密，公钥解密；

```js
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');
const root = __dirname;

const publicKey = fs.readFileSync(path.join(root, '../rsa_public_key.pem')).toString('ascii');
const privateKey = fs.readFileSync(path.join(root, '../rsa_private_key.pem')).toString('ascii');
const data = 'data to crypt';

// 公钥加密
const encryptData = crypto.publicEncrypt(publicKey, Buffer.from(data)).toString('base64');
console.log('encode', encryptData);
// 私钥解密
const decryptData = crypto.privateDecrypt(privateKey, Buffer.from(encryptData.toString('base64'), 'base64'));
console.log('decode', decryptData.toString());

// 私钥加密
const encryptData2 = crypto.privateEncrypt(privateKey, Buffer.from(data)).toString('base64');
console.log('encode', encryptData2);
// 公钥解密
const decryptData2 = crypto.publicDecrypt(publicKey, Buffer.from(encryptData2.toString('base64'), 'base64'));
console.log('decode', decryptData2.toString());
```

1. 当然，这里的公钥可以使用OpenSSL产生的，也可以用上一步产生的公钥私钥

```
const crypto = require('crypto');
const { publicKey, privateKey } = crypto.generateKeyPairSync('rsa', {
  modulusLength: 1024,
  publicKeyEncoding: {
    type: 'pkcs1',
    format: 'pem'
  },
  privateKeyEncoding: {
    type: 'pkcs1',
    format: 'pem',
    // cipher: 'aes-256-cbc',
    // passphrase: 'top secret'
  }
});
const data = 'data to crypt';

// 公钥加密
const encryptData = crypto.publicEncrypt(publicKey, Buffer.from(data)).toString('base64');
console.log('encode', encryptData);
// 私钥解密
const decryptData = crypto.privateDecrypt(privateKey, Buffer.from(encryptData.toString('base64'), 'base64'));
console.log('decode', decryptData.toString());
```

## 四、Jsrsasign

```
npm i jsrsasign
npm i jsrsasign-util
```

- 优点
  加密解密方法比较全，支持nodejs的crypto模块生产的pkcs1格式的公钥；
- 缺点
  生成rsa密钥耗时较长，加密解密过程耗时较长；

```
nodejs和web端均可使用
```

1. 支持openssl及nodersa生产的标准格式的rsa密钥

```js
const fs = require('fs')
var rs = require('jsrsasign');
const path = require('path');
const root = __dirname;

const publicKey = fs.readFileSync(path.join(root, './rsa_public_key.pem')).toString('ascii');
const privateKey = fs.readFileSync(path.join(root, './rsa_private_key.pem')).toString('ascii');

// 加密
// 读取解析pem格式的秘钥, 生成秘钥实例 (RSAKey) 
var date1 = new Date().getTime()
var pub = rs.KEYUTIL.getKey(publicKey);
var enc = rs.KJUR.crypto.Cipher.encrypt('src', pub);
console.log(enc);
//38b5835801da14a7efb1d15ee7c92d87e12b5535de29335401a7df4fc190ca251beeff6fa5f8bd3be47cefea6533dcd7bdc1120dcad91504b5b428d5c5ac20446c6849511081b96dbb67668dd81f42b3c46e5a68b52c85a26feee49d4f62b3f8f39e6feaaffc91a8d637a697d63b2e68806ce8919c1133c44fbf2d99c91c6650
console.log(rs.hextob64(enc));
//OLWDWAHaFKfvsdFe58kth+ErVTXeKTNUAaffT8GQyiUb7v9vpfi9O+R87+plM9zXvcESDcrZFQS1tCjVxawgRGxoSVEQgbltu2dmjdgfQrPEblpotSyFom/u5J1PYrP4855v6q/8kajWN6aX1jsuaIBs6JGcETPET78tmckcZlA=
// 解密
var prv = rs.KEYUTIL.getKey(privateKey);
var dec = rs.KJUR.crypto.Cipher.decrypt(enc, prv);
console.log(new Date().getTime() - date1)
console.log("jsrsasign decrypt: " + dec);
//jsrsasign decrypt:src
```

1. 支持jsrsasign生产的密钥

```js
var rs = require('jsrsasign');
const path = require('path');
const root = __dirname;
// 生产密钥对
var date1 = new Date().getTime()
var rsaKeypair = rs.KEYUTIL.generateKeypair("RSA", 512);
console.log(new Date().getTime() - date1) //314
// 密钥对象获取pem格式的密钥
var pub = rs.KEYUTIL.getPEM(rsaKeypair.pubKeyObj);
var prv = rs.KEYUTIL.getPEM(rsaKeypair.prvKeyObj,'PKCS8PRV');
console.log(pub);
// -----BEGIN PUBLIC KEY-----
// MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAMBAvyyQ7ZvG+vKmAvb3zrA816O5i0tg
// FWKaveQbuZ/xwFdHRFB/f27pEKo7jbUWBMDDIBGNu7QZ/uG6birsJ0UCAwEAAQ==
// -----END PUBLIC KEY-----
console.log(prv);
// -----BEGIN PRIVATE KEY-----
// MIIBVgIBADANBgkqhkiG9w0BAQEFAASCAUAwggE8AgEAAkEAt1huGEUWoa1z2Ruw
// DRdJX+EWfZI64QNJ8UUVwSyEou7PB0fwTdczr5UbxjH2zRsNQo5wPHAOTKvr2GGh
// DH1nFwIDAQABAkBBjwM+9mVTRnxoI3heFfeMqyWpnQIkt1JXTUasHkkHIRVaZMFx
// b5rnSRSpj/QzWHUAT9CaffZqRoqM9EWig2wRAiEA8WRJZpvac7CK0OPvE8sD2THZ
// D49fzyvNf9dRZUI9IAsCIQDCcN9XASIhFOkrrVC9ySr4+m+VTlJfVKO6P95/7V9A
// pQIhALrPH7bW2mI5t9Qc8YJh1GKbnx3ZmQ3dGjXbTlSMxH0tAiEAlvMFkAfjNQeE
// 1VGhwxSvdccGZUT+kd+lk+wNkgb30bkCIQDwS8xr2P4bm3vkVBCDvrttjTKPIjtG
// apnvPZ2yh1OIqQ==
// -----END PRIVATE KEY-----
// 加密
// 读取解析pem格式的秘钥, 生成秘钥实例 (RSAKey) 
var date1 = new Date().getTime()
// 公钥加密
var enc = rs.KJUR.crypto.Cipher.encrypt('src', rsaKeypair.pubKeyObj);
console.log(enc);
//5ee71af15aa5fcc652d8ccaab81a1883e49232b4726c31a538ed98347de972c3f86cb736fa4622f14c7bb872b33ebc0bb57657dcdddc22992c27d1c71600a8f4
console.log(rs.hextob64(enc));
// Xuca8Vql/MZS2MyquBoYg+SSMrRybDGlOO2YNH3pcsP4bLc2+kYi8Ux7uHKzPrwLtXZX3N3cIpksJ9HHFgCo9A==

// 私钥解密
//    var enc=rs.hextob64(enc)
var dec = rs.KJUR.crypto.Cipher.decrypt(enc, rsaKeypair.prvKeyObj);
console.log("jsrsasign decrypt: " + dec);
// jsrsasign decrypt: src
```

1. 支持crypto生产的密钥，并且加密方式和crypto的解密方式通用
   `(需要指定生成密钥对的填充方式，及解密时私钥的填充方式为pkcs1）`

```js
var crypto = require('crypto');
var rs = require('jsrsasign');

date1 = new Date().getTime()
const { publicKey, privateKey } = crypto.generateKeyPairSync('rsa', {
    modulusLength: 512,
    publicKeyEncoding: {
        type: 'spki',
        format: 'pem'
    },
    privateKeyEncoding: {
        type: 'pkcs1',
        format: 'pem',
        // cipher: 'aes-256-cbc',
        // passphrase: 'top secret'
    }
});
console.log(new Date().getTime() - date1)

var date1 = new Date().getTime()
var pub = rs.KEYUTIL.getKey(publicKey);
var enc = rs.KJUR.crypto.Cipher.encrypt('src', pub);
// console.log(enc);
console.log(rs.hextob64(enc));
//DZzrNuD730u+5AsRcikeSMoU1yCscUutQlqXJ5WU/2iUSuCsJxyOJOJjVbI87r7EGL7EjeS278PziN9pZ7TWOg==

// 使用jsrsasign解密
var prv = rs.KEYUTIL.getKey(privateKey);
var dec = rs.KJUR.crypto.Cipher.decrypt(enc, prv);
console.log(new Date().getTime() - date1)
console.log("jsrsasign decrypt: " + dec);
//jsrsasign decrypt:src
// 使用crypto模块解密
var enc = rs.hextob64(enc)
const dec2 = crypto.privateDecrypt({
    key: privateKey,
    padding: crypto.constants.RSA_PKCS1_PADDING
}, Buffer.from(enc.toString('base64'), 'base64')).toString();
console.log("jsrsasign decrypt: " + dec2);
//jsrsasign decrypt:src
```

## 五、JSEncrypt

```
npm i node-jsencrypto
```

Web直接引入 `html文件引入jsencrypt.min.js（https://github.com/travist/jsencrypt）`

- 优点:
  支持rsa加密解密，支持标准的rsa密钥；比jsrsasign加密速度稍微快几毫秒；加密内容可以用nodejs标准的加密模块crypto解密
- 缺点：
  不能生产rsa密钥；

```
nodejs和web端均可使用
```

### DEMO

1. nodejs端node-jsencrypt

```js
var crypto = require('crypto');

date1 = new Date().getTime()
var { publicKey, privateKey } = crypto.generateKeyPairSync('rsa', {
    modulusLength: 512,
    publicKeyEncoding: {
        type: 'spki',
        format: 'pem'
    },
    privateKeyEncoding: {
        type: 'pkcs1',
        format: 'pem',
        // cipher: 'aes-256-cbc',
        // passphrase: 'top secret'
    }
});
console.log(new Date().getTime() - date1)
// 引入加密模块
JSEncrypt = require('node-jsencrypt')
// 加密
date1 = new Date().getTime()
const jsEncrypt = new JSEncrypt()
jsEncrypt.setPublicKey(publicKey)
enc = jsEncrypt.encrypt('src')
console.log(new Date().getTime() - date1)
//解密
date1 = new Date().getTime()
var decrypt = new JSEncrypt();
decrypt.setPrivateKey(privateKey);
var uncrypted = decrypt.decrypt(enc);
console.log(new Date().getTime() - date1)
// 使用crypto模块解密
const dec3 = crypto.privateDecrypt({
    key: privateKey,
    padding: crypto.constants.RSA_PKCS1_PADDING
}, Buffer.from(enc.toString('base64'), 'base64')).toString();
console.log("jsrsasign decrypt: " + dec2);
```

1. web端JSEncrypt

```html
<!DOCTYPE html>
<html>
  <head>
    <title>JavaScript RSA Encryption</title>
	<script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>
  	<script src="https://cdn.bootcdn.net/ajax/libs/jsencrypt/3.0.0-rc.1/jsencrypt.min.js"></script>
    <script type="text/javascript">
 
      // Call this code when the page is done loading.
      $(function() {
 
        // Run a quick encryption/decryption when they click.
        $('#testme').click(function() {
 
          // Encrypt with the public key...
          var encrypt = new JSEncrypt();
          encrypt.setPublicKey($('#pubkey').val());
          var encrypted = encrypt.encrypt($('#input').val());
 
          // Decrypt with the private key...
          var decrypt = new JSEncrypt();
          decrypt.setPrivateKey($('#privkey').val());
          var uncrypted = decrypt.decrypt(encrypted);
 
          // Now a simple check to see if the round-trip worked.
          if (uncrypted == $('#input').val()) {
            alert('It works!!!');
          }
          else {
            alert('Something went wrong....');
          }
        });
      });
    </script> 
  </head>
  <body>
    <label for="privkey">Private Key</label><br/>
    <textarea id="privkey" rows="15" cols="65">-----BEGIN RSA PRIVATE KEY-----
MIICXQIBAAKBgQDlOJu6TyygqxfWT7eLtGDwajtNFOb9I5XRb6khyfD1Yt3YiCgQ
WMNW649887VGJiGr/L5i2osbl8C9+WJTeucF+S76xFxdU6jE0NQ+Z+zEdhUTooNR
aY5nZiu5PgDB0ED/ZKBUSLKL7eibMxZtMlUDHjm4gwQco1KRMDSmXSMkDwIDAQAB
AoGAfY9LpnuWK5Bs50UVep5c93SJdUi82u7yMx4iHFMc/Z2hfenfYEzu+57fI4fv
xTQ//5DbzRR/XKb8ulNv6+CHyPF31xk7YOBfkGI8qjLoq06V+FyBfDSwL8KbLyeH
m7KUZnLNQbk8yGLzB3iYKkRHlmUanQGaNMIJziWOkN+N9dECQQD0ONYRNZeuM8zd
8XJTSdcIX4a3gy3GGCJxOzv16XHxD03GW6UNLmfPwenKu+cdrQeaqEixrCejXdAF
z/7+BSMpAkEA8EaSOeP5Xr3ZrbiKzi6TGMwHMvC7HdJxaBJbVRfApFrE0/mPwmP5
rN7QwjrMY+0+AbXcm8mRQyQ1+IGEembsdwJBAN6az8Rv7QnD/YBvi52POIlRSSIM
V7SwWvSK4WSMnGb1ZBbhgdg57DXaspcwHsFV7hByQ5BvMtIduHcT14ECfcECQATe
aTgjFnqE/lQ22Rk0eGaYO80cc643BXVGafNfd9fcvwBMnk0iGX0XRsOozVt5Azil
psLBYuApa66NcVHJpCECQQDTjI2AQhFc1yRnCU/YgDnSpJVm1nASoRUnU8Jfm3Oz
uku7JUXcVpt08DFSceCEX9unCuMcT72rAQlLpdZir876
-----END RSA PRIVATE KEY-----</textarea><br/>
    <label for="pubkey">Public Key</label><br/>
    <textarea id="pubkey" rows="15" cols="65">-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDlOJu6TyygqxfWT7eLtGDwajtN
FOb9I5XRb6khyfD1Yt3YiCgQWMNW649887VGJiGr/L5i2osbl8C9+WJTeucF+S76
xFxdU6jE0NQ+Z+zEdhUTooNRaY5nZiu5PgDB0ED/ZKBUSLKL7eibMxZtMlUDHjm4
gwQco1KRMDSmXSMkDwIDAQAB
-----END PUBLIC KEY-----</textarea><br/>
    <label for="input">Text to encrypt:</label><br/>
    <textarea id="input" name="input" type="text" rows=4 cols=70>This is a test!</textarea><br/>
    <input id="testme" type="button" value="Test Me!!!" /><br/>
  </body>
</html>
```