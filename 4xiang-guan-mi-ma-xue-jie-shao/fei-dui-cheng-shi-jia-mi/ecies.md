# ECIES\( Elliptic Curve Integrated Encryption Scheme \)

為橢圓曲線\(ECC\)結合IES，其中IES全名為**Integrated Encryption Scheme**是一種結合非對稱式加密與對稱式加密的機制。

當小明要送一個加密訊息給阿煌時他需要知道如下資料 :

```
1. Key derivation function (e.g., ANSI-X9.63-KDF with SHA-1 option)

2. Message authentication code (e.g., HMAC-SHA-1-160 with 160-bit keys or HMAC-SHA-1-80 with 80-bit keys)

3. Symmetric encryption scheme (e.g., TDEA in CBC mode or XOR encryption scheme) 當作 E;

4. Elliptic curve domain parameters: (p,a,b,G,n,h)

5. 阿煌的 Public key: K ，K為阿煌的私鑰與G的乘積。
```

程式範例:

```js
const crypto = require('crypto');
const secp256k1 = require("secp256k1");


function sha512(msg) {
  return crypto.createHash("sha512").update(msg).digest();
}

function aes256CbcEncrypt(iv, key, plaintext) {
  let cipher = crypto.createCipheriv("aes-256-cbc", key, iv);
  let firstChunk = cipher.update(plaintext);
  let secondChunk = cipher.final();
  return Buffer.concat([firstChunk, secondChunk]);
}

function aes256CbcDecrypt(iv, key, ciphertext) {
  let cipher = crypto.createDecipheriv("aes-256-cbc", key, iv);
  let firstChunk = cipher.update(ciphertext);
  let secondChunk = cipher.final();
  return Buffer.concat([firstChunk, secondChunk]);
}

function hmacSha256(key, msg) {
  return crypto.createHmac("sha256", key).update(msg).digest();
}


const getPublic = function(privateKey) {
  if(privateKey.length !== 32) {
    console.log("Private key length need to be 32");
    return
  }
  let compressed = secp256k1.publicKeyCreate(privateKey);
  return secp256k1.publicKeyConvert(compressed, false);
};

const derive = function(privateKeyA, publicKeyB) {
  const _derive = crypto.createECDH('secp256k1');
  _derive.setPrivateKey(privateKeyA);
  return new Promise(function(resolve) {
    resolve(
      _derive.computeSecret(publicKeyB, null, 'hex'))
  });
};


const encrypt = function(publicKeyTo, msg, opts) {
  opts = opts || {};
  let ephemPublicKey;
  return new Promise(function(resolve) {
    let ephemPrivateKey = opts.ephemPrivateKey || crypto.randomBytes(32);
    ephemPublicKey = getPublic(ephemPrivateKey);
    resolve(derive(ephemPrivateKey, publicKeyTo));
  }).then(function(Px) {
    let hash = sha512(Px);
    let iv = opts.iv || crypto.randomBytes(16);
    let encryptionKey = hash.slice(0, 32);
    let macKey = hash.slice(32);
    let ciphertext = aes256CbcEncrypt(iv, encryptionKey, msg);
    let dataToMac = Buffer.concat([iv, ephemPublicKey, ciphertext]);
    let mac = hmacSha256(macKey, dataToMac);
    return {
      iv: iv,
      ephemPublicKey: ephemPublicKey,
      ciphertext: ciphertext,
      mac: mac,
    };
  });
};

const decrypt = function(privateKey, opts) {
  return derive(privateKey, opts.ephemPublicKey).then(function(Px) {
    let hash = sha512(Px);
    let encryptionKey = hash.slice(0, 32);
    let macKey = hash.slice(32);
    let dataToMac = Buffer.concat([
      opts.iv,
      opts.ephemPublicKey,
      opts.ciphertext
    ]);
    return aes256CbcDecrypt(opts.iv, encryptionKey, opts.ciphertext);
  });
};

const privateKey_yicheng = crypto.randomBytes(32);
const publicKey_yicheng = getPublic(privateKey_yicheng);

encrypt(publicKey_yicheng, Buffer("I have one banana.")).then(function(encrypted) {
  console.log('Encrypted payload')
  console.log(encrypted)
  console.log('-------------------')
  decrypt(privateKey_yicheng, encrypted).then(function(plaintext) {
    console.log("Message to yicheng:", plaintext.toString());
  });
})
```

> 參考至:
>
> [https://en.wikipedia.org/wiki/Integrated\_Encryption\_Scheme](https://www.gitbook.com/book/easonwang01/e/edit#)
>
> [https://github.com/bitchan/eccrypto](https://github.com/bitchan/eccrypto)



