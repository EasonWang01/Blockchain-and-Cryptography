# 一般地址

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

[https://gist.github.com/lgn21st/1bd2db7eb30b55e17d07a8bc637c2f87](https://gist.github.com/lgn21st/1bd2db7eb30b55e17d07a8bc637c2f87)

# 合約地址

合約地址會依據部屬合約的地址以及該地址總共部屬第幾個合約而定。

規則如下 :

固定格式為

```
d6 + 94 + 部屬人之地址 + nonce
```

如果該帳號是第一次部屬合約則 nonce 為80，第二次部屬合約 nonce 為01之後則繼續往上03, 04...

> 如果是在mainnet則 nonce 從1開始
>
> [https://github.com/ethereum/EIPs/blob/master/EIPS/eip-161.md\#specification](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-161.md#specification)

範例 :

```js
const SHA3 = require('keccakjs')

function getContract_Address(address, num) {
  let nonce;
  if(num === 0) {
    nonce = 80;
  } else if (num < 10) {
    nonce = `0${num}`;
  }

  let h = new SHA3(256).update(Buffer.from("d6" + "94" + address + nonce, 'hex')).digest('hex');
  let contractAddress = h.slice(-40) // 取後面四十個字
  console.log('-------------------------------------')
  console.log('Contract Address: ')
  console.log(`0x${contractAddress}`)
}

getContract_Address("d7c86c344ecbd9f166b053a32cd6cd34dda1b8af", 0)
getContract_Address("d7c86c344ecbd9f166b053a32cd6cd34dda1b8af", 1)
getContract_Address("d7c86c344ecbd9f166b053a32cd6cd34dda1b8af", 2)
```

![](/assets/fefw24.png)對照上圖即可看到右下角之Remix IDE 產生

