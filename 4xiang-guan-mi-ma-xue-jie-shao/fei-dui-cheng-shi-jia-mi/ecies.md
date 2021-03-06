# ECIES \( Elliptic Curve Integrated Encryption Scheme \)

為橢圓曲線 \( ECC \) 結合IES，其中IES全名為 **Integrated Encryption Scheme **是一種結合非對稱式加密與對稱式加密的機制。

當小明要送一個加密訊息給阿煌時他需要知道如下資料 :

```
1. Key derivation function (e.g., ANSI-X9.63-KDF with SHA-1 option)

2. Message authentication code (e.g., HMAC-SHA-1-160 with 160-bit keys or HMAC-SHA-1-80 with 80-bit keys)

3. Symmetric encryption scheme (e.g., TDEA in CBC mode or XOR encryption scheme) 

4. Elliptic curve domain parameters: (p,a,b,G,n,h)

5. 阿煌的 Public key: K ，K為阿煌的私鑰與G的乘積。
```

#### 加密步驟:

```
1. 使用Public key 與Private Key 來 derive出 Share Secret

2. 把Share Secret做SHA512雜湊

3. 產生16 bytes隨機IV

4. 把第二步驟的值取前32位當encryptionKey

5. 把第二步驟的值取後32位當macKey

6. 把 iv, encryptionKey 一起使用 AES-256-CBC 加密，產生ciphertext。
```

#### 解密步驟:

```
1. 使用Public key 與Private Key 來 derive出 Share Secret

2. 之後跟加密步驟一樣，經過Share secret雜湊後取前32位當作encryptionKey，而後32位當作macKey

3. 驗證加密時算出的MAC值是否與解密時算的MAC值符合

4. 傳入iv、encryptionKey、ciphertext，使用AES-256-CBC解密
```

#### 程式範例:

```js
const crypto = require('crypto');
const secp256k1 = require("secp256k1");

// sha256雜湊
function sha512(msg) {
  return crypto.createHash("sha512").update(msg).digest();
}
// AES-256-CBC加密
function aes256CbcEncrypt(iv, key, plaintext) {
  let cipher = crypto.createCipheriv("aes-256-cbc", key, iv);
  let firstChunk = cipher.update(plaintext);
  let secondChunk = cipher.final();
  return Buffer.concat([firstChunk, secondChunk]);
}
// AES-256-CBC解密
function aes256CbcDecrypt(iv, key, ciphertext) {
  let cipher = crypto.createDecipheriv("aes-256-cbc", key, iv);
  let firstChunk = cipher.update(ciphertext);
  let secondChunk = cipher.final();
  return Buffer.concat([firstChunk, secondChunk]);
}
// SHA256雜湊訊息驗證碼
function hmacSha256(key, msg) {
  return crypto.createHmac("sha256", key).update(msg).digest();
}
// 從Private Key產生Public Key
const getPublic =  function(privateKey) {
  if(privateKey.length !== 32) {
    console.log("Private key length need to be 32");
    return
  }
  let compressed = secp256k1.publicKeyCreate(privateKey);
  return secp256k1.publicKeyConvert(compressed, false);
};
//產生Share Secret
const derive =  function(privateKeyA, publicKeyB) {
  const _derive = crypto.createECDH('secp256k1');
  _derive.setPrivateKey(privateKeyA);
  return new Promise(function(resolve) {
    resolve(
      _derive.computeSecret(publicKeyB, null, 'hex'))
  });
};

// 比較Mac值
function equalConstTime(b1, b2) {
  if (b1.length !== b2.length) {
    return false;
  }
  var res = 0;
  for (var i = 0; i < b1.length; i++) {
    res |= b1[i] ^ b2[i];  
  }
  return res === 0;
}

// 加密
const encrypt = function(publicKeyTo, msg, opts) {
  opts = opts || {};
  let ephemPublicKey;
  return new Promise(function(resolve) {
    let ephemPrivateKey = opts.ephemPrivateKey || crypto.randomBytes(32);
    ephemPublicKey = getPublic(ephemPrivateKey);
    // 1.使用Public key 與Private Key 來 derive出 Share Secret
    resolve(derive(ephemPrivateKey, publicKeyTo));
  }).then(function(Px) {
    // 2.把Share Secret做SHA512雜湊
    let hash = sha512(Px);
    // 3.產生16 bytes隨機IV
    let iv = opts.iv || crypto.randomBytes(16);
    // 4. 把第二步驟的值取前32位當encryptionKey
    let encryptionKey = hash.slice(0, 32);
    // 5. 把第二步驟的值取後32位當macKey
    let macKey = hash.slice(32);
    // 6. 把iv, encryptionKey, 訊息一起使用AES-256-CBC加密。
    let ciphertext = aes256CbcEncrypt(iv, encryptionKey, msg);
    let dataToMac = Buffer.concat([iv, ephemPublicKey, ciphertext]);
    let mac = hmacSha256(macKey, dataToMac);
    return {
      iv,
      ephemPublicKey,
      ciphertext,
      mac,
    };
  });
};
// 解密
const decrypt = function(privateKey, opts) {
    // 1.使用Public key 與Private Key 來 derive出 Share Secret
  return derive(privateKey, opts.ephemPublicKey).then(function(Px) {
    // 2.之後跟加密步驟一樣，經過Share secret雜湊後取前32位當作encryptionKey
    //   而後32位當作macKey
    let hash = sha512(Px);
    let encryptionKey = hash.slice(0, 32);
    let macKey = hash.slice(32);
    let dataToMac = Buffer.concat([
      opts.iv,
      opts.ephemPublicKey,
      opts.ciphertext
    ]);
    // 驗證MAC是否符合
    var realMac = hmacSha256(macKey, dataToMac);
    if(!equalConstTime(opts.mac, realMac)) {
      console.log("MAC not match");
      return
    }
    // 使用AES-256-CBC解密
    return aes256CbcDecrypt(opts.iv, encryptionKey, opts.ciphertext);
  });
};

// 產生私鑰與公鑰
const privateKey_yicheng = crypto.randomBytes(32);
const publicKey_yicheng = getPublic(privateKey_yicheng);

// 執行加密與解密
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



