```js
var crypto = require('crypto');
var ecdh = crypto.createECDH('secp256k1');
const SHA3 = require('keccakjs')

console.log(sha3.keccak256('123'))

var hash2 = crypto.randomBytes(32)
console.log('--------')
console.log('私鑰')
console.log(hash2.toString('hex')); //私鑰，64位十六進制數 //使用hash2.toString('hex')即可看到16進位字串
console.log('--------')


// ECDH和ECDSA產生公私鑰的方式都相同
var publickey = ecdh.setPrivateKey(hash2.toString('hex'),'hex').getPublicKey('hex')
console.log('公鑰')
console.log(publickey); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)
console.log('--------')


let pubKeyBuf = new Buffer(publickey, 'hex')
let h = new SHA3(256)
h.update(pubKeyBuf.slice(1)) // 130 => 128

let address = h.digest('hex').slice(-40)

console.log(address)
```



