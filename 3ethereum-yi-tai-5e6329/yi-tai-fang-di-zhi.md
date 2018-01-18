使用原生模組搭配SHA3模組產生地址

```js
const crypto = require('crypto');
const ecdh = crypto.createECDH('secp256k1');
const SHA3 = require('keccakjs')

const random_hash = crypto.randomBytes(32)
console.log('-------------------------------------')
console.log('私鑰Private Key: ')
console.log(random_hash.toString('hex')); //私鑰，32bytes 十六進制數
console.log('-------------------------------------')

// 產生公鑰
const publickey = ecdh.setPrivateKey(random_hash).getPublicKey('hex')
console.log('公鑰Public Key: ')
console.log(publickey); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)
// 產生地址
let pubKeyBuf = new Buffer(publickey, 'hex')
let h = new SHA3(256)
h.update(pubKeyBuf.slice(1)) // 把Buffer前第一個部分移除 130 => 128
let address = h.digest('hex').slice(-40) // 取後面四十個字

console.log('-------------------------------------')
console.log('地址Address: ')
console.log(`0x${address}`)
```

使用第三方模組 \( ethereumjs-wallet \) 產生地址:

```js
const crypto = require('crypto')
const eth = require('ethereumjs-wallet')
const random_hash = crypto.randomBytes(32)
const instance = eth.fromPrivateKey(random_hash)

console.log('private key: ')
console.log(instance.getPrivateKey().toString('hex'))
console.log('-------------------------------------')
console.log('public key: ')
console.log(instance.getPublicKey().toString('hex'))
console.log('-------------------------------------')
console.log('Address: ')
console.log(`0x${instance.getAddress().toString('hex')}`)
```

其他語言可參考:

https://gist.github.com/lgn21st/1bd2db7eb30b55e17d07a8bc637c2f87

