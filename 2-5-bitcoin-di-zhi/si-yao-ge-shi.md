# WIF\(Wallet import format\)

一般私鑰跟WIF格式私鑰可以互相轉換，其優點在於長度稍微縮短，以及加入了checksum

可參考以下網站在線轉換[http://gobittest.appspot.com/PrivateKey](http://gobittest.appspot.com/PrivateKey)

## 一般私鑰轉為WIF格式

```js
var crypto = require('crypto');
var bs58 = require('bs58');

// 隨機的私鑰
const privateKey = "0C28FCA386C7A227600B2FE50B7CAE11EC86D3BF1FBE471BE89827E19D72AA1D";

// 前面加上0x80
var add0x80_front = "80" + privateKey;

// 使用double sha256   後面.update使用hex原因是nodejs預設v6版本之後預設是utf-8
// https://stackoverflow.com/questions/37213211/why-crypto-createhash-returns-different-output-in-new-version
const sha256Value1 = crypto.createHash('sha256').update(add0x80_front, 'hex').digest();
const sha256Value2 = crypto.createHash('sha256').update(sha256Value1, 'hex').digest();

// 取前八位當作checksum
const first4byte = sha256Value2.toString('hex').substring(0,8);

// 串接
const concatString = add0x80_front + first4byte;
WIF_PrivateKey = bs58.encode(new Buffer(concatString, 'hex'));
console.log(WIF_PrivateKey)
```

## WIF格式私鑰轉換為原本私鑰格式

```js
var crypto = require('crypto');
var bs58 = require('bs58');

// WIF格式之私鑰
const WIF_privateKey = "5HueCGU8rMjxEXxiPuD5BDku4MkFqeZyd4dZ1jvhTVqvbTLvyTJ";

// base58 decode
const decodeWIF = bs58.decode(WIF_privateKey);

// 把最後四位checksum丟棄
const dropLast4 = decodeWIF.slice(0,33);

// 把第一個byte丟棄
const final = decodeWIF.slice(1, dropLast4.length);
console.log(final)
```



