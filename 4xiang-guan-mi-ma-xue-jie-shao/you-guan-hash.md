# 有關Hash

Hash function也可稱為**雜湊函式，**主要功能是把一串不定長度大小的字串或數字，轉換一串為固定大小的值。

而任何相同的值經過hash過後出來的值都會是相同的，所以就可以進一步做到訊息驗證。

通常是一個單向函式，也就是無法從算出來的值反推回原本的值 \(除了已經被破解的Hash算法例外\)。

比較常見的包含MD5、SHA1、SHA2、SHA3與Ripemd系列等等，但目前MD5與SHA1已經被破解。

而還有一些是專門用來幫密碼做Hash的雜湊函式，例如：

[bcrypt](https://www.gitbook.com/book/easonwang01/e/edit#)、[PBKDF2](https://www.gitbook.com/book/easonwang01/e/edit#)、[scrypt](https://www.gitbook.com/book/easonwang01/e/edit#)、[Argon2](https://www.gitbook.com/book/easonwang01/e/edit#)等等。

我們下面一樣使用Node.js來展示一些Hash function讓大家可以快速地了解他們的用法。

## MD5

```js
var crypto = require('crypto');
function md5 (text) {
  return crypto.createHash('md5').update(text).digest('hex');
};

let hash = md5('test');
console.log(hash);
```

## SHA

全名為Secure Hash Algorithm，從1995年發佈的SHA1、2001年發佈的SHA2、2015年正式發佈的SHA3，每一代都是上一代的改進版本，而SHA1之hash過後的值長度為160bits，到了SHA2與SHA3他們的長度可以是以下幾種，例如：224、256、384、512等等，所以我們才會常聽到例如SHA-256或是SHA-512等等的名詞。

SHA-256範例：

```js
var crypto = require('crypto');
let hash = crypto.createHash('sha256').update('test').digest('hex'); //hex代表輸出為16進位，原先為buffer型態
console.log(hash);
```

#### SHA3

而在2015年8月5日SHA-3正式發表，而由於原先的Keccak被選為SHA3的最適合算法，所以SHA3也稱為Keccak

以下為Node.js SHA3範例：

```
npm install sha3
```

```js
var SHA3 = require('sha3');

let hash_512 = new SHA3.SHA3Hash().update('test').digest('hex'); //預設為512bits
console.log(hash_512)

let hash_224 = new SHA3.SHA3Hash(224).update('test').digest('hex'); // 224bits
console.log(hash_224)
```

#### 

## Ripemd

Ripemd系列包含128、160、256、320等等，也就是他的hash過後的長度，最常見的是Ripemd-160，也就是產生160bits長度的Hash，而Ripemd主要為了加強與改良原先的MD系列而發明。

Ripemd-160範例：

> 需要先安裝 ripemd160 第三方模組
>
> ```
> npm install ripemd160
> ```

```js
var RIPEMD160 = require('ripemd160')

console.log(new RIPEMD160().update('test').digest('hex'))
```

## HMAC

HMAC是在上述的雜湊函式外再加上一個secret，然後一起進行Hash。

```js
const crypto = require('crypto');
const hmac = crypto.createHmac('sha256', 'a secret');

hmac.update('test');
console.log(hmac.digest('hex'));
```

## Blake2

https://blake2.net/

這是一個強調快速、安全與簡單的Hash算法，並且目前被用來替代MD5和SHA1。

因為它可以在不耗費更多資源的情況下提供更好的安全性，但後來由Keccak獲選為SHA3的原因是NIST認為Keccak更具有抗ASIC的能力以及因為Blake的實作類似於SHA2，所以後來才不選擇Blake為SHA3標準。



