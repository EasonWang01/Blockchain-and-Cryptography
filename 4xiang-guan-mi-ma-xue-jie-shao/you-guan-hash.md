# 有關Hash

Hash function也可稱為**雜湊函式，**主要功能是把一串不定長度大小的字串或數字，轉換一串為固定大小的值。

而任何相同的值經過hash過後出來的值都會是相同的，所以就可以進一步做到訊息驗證。

通常是一個單向函式，也就是無法從算出來的值反推回原本的值 \(除了已經被破解的Hash算法例外\)。

比較常見的包含MD5、SHA1、SHA2、SHA3與ripemd系列等等，但目前MD5與SHA1已經被破解。

而還有一些是專門用來幫密碼做Hash的雜湊函式，例如：

[bcrypt](https://www.gitbook.com/book/easonwang01/e/edit#)、[PBKDF2](https://www.gitbook.com/book/easonwang01/e/edit#)、[scrypt](https://www.gitbook.com/book/easonwang01/e/edit#)、[Argon2](https://www.gitbook.com/book/easonwang01/e/edit#)等等。



我們下面一樣使用Node.js來展示一些Hash function讓大家可以快速地了解他們的用法。



#### MD5

```js
var crypto = require('crypto');
function md5 (text) {
  return crypto.createHash('md5').update(text).digest('hex');
};

let hash = md5('test');
console.log(hash);
```



