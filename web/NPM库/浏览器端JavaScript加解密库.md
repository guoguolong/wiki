## crypto-js（AES、md5等)

https://www.npmjs.com/package/crypto-js

```
npm install crypto-js
```

实现 AES、md5、sha等加解密

## cryptico（RSA/AES 加解密）

```
var cryptico = require('cryptico');

var PassPhrase = "The Moon is a Harsh Mistress."; 
var Bits = 1024; 
var AllenRSAkey = cryptico.generateRSAKey(PassPhrase, Bits);
var AllenPublicKeyString = cryptico.publicKeyString(AllenRSAkey);

console.log('Public Key: ', AllenPublicKeyString)

// 开始RAS 加解密

var PlainText = "Allen, I need you to help me with my Starcraft strategy.";
 
var CipherTextObj = cryptico.encrypt(PlainText, AllenPublicKeyString);
var PlainTextObj = cryptico.decrypt(CipherTextObj.cipher, AllenRSAkey);
console.log('Plain Text:', PlainTextObj.plaintext)
```

## crypto 在线小工具

http://encode.chahuo.com/