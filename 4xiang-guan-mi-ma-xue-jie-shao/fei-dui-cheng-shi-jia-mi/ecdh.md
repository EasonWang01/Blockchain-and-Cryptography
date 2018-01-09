# ECDH

為橢圓曲線結合Diffie-Hellman\(DH\)之加密演算法，全名為Elliptic Curve Diffie-Hellman \(ECDH\)。

[https://koclab.cs.ucsb.edu/teaching/ecc/project/2015Projects/Haakegaard+Lang.pdf](https://koclab.cs.ucsb.edu/teaching/ecc/project/2015Projects/Haakegaard+Lang.pdf)

用途:

```
讓兩人不透漏私密訊息的情況下獲得一把共同的密鑰。
```

#### 原理步驟:

1.阿東與小明先約定好，選擇一個共同的橢圓曲線 

$$y^2 = (x^3 + ax + b)$$ mod p 之 a, b, p 之三個參數

2.之後兩人公同約定一個曲線上的點當作公鑰

3.阿東把自己的私密數字乘上公鑰後傳給小明，而小明把自己的私密數字乘上公鑰後傳給阿東

4.雙方把各自拿到的對方結果再乘上自己的私密數字即可算出一個共享的密鑰。



## Node.js範例

```js
const crypto = require('crypto');

// A
const ecdh_A = crypto.createECDH('secp256k1');
ecdh_A.generateKeys();
const publicKey_A = ecdh_A.getPublicKey(null, 'compressed');

// B
const ecdh_B = crypto.createECDH('secp256k1');
ecdh_B.generateKeys();
const publicKey_B = ecdh_B.getPublicKey(null, 'compressed');

const secret1 = ecdh_A.computeSecret(publicKey_B).toString('hex');
console.log('Secret1: ', secret1.length, secret1);

const secret2 = ecdh_B.computeSecret(publicKey_A).toString('hex');
console.log('Secret2: ', secret2.length, secret2);

console.log(`兩者Secret相同: ${secret1 === secret2}`)
```



