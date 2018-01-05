# BlowFish

Blowfish 是一種 block cipher在1993年由 Bruce Schneier發表，此加密演算法現今已不再安全，不推薦使用。

其具有64-bit 的 block size 而其key長度可為 32 bits 至 448 bits 。

其運算過程如下圖

![](/assets/sds.png)

> [https://en.wikipedia.org/wiki/Blowfish\_\(cipher\](https://en.wikipedia.org/wiki/Blowfish_%28cipher\)\)

其運算過程敘述可參考:https://en.wikipedia.org/wiki/Blowfish\_\(cipher\)\#The\_algorithm

Node.js範例:

```js
const crypto = require('crypto');

const mode = 'blowfish';

// 加密
const cipher = crypto.createCipher(mode, 'a password');
let encrypted = cipher.update('I_am_plaintext', 'utf8', 'hex');
encrypted += cipher.final('hex');
console.log(encrypted);

// 解密
const decipher = crypto.createDecipher(mode, 'a password');
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');
console.log(decrypted);
```

# TwoFish

Twofish 是一種 block cipher 由 Counterpane Labs 在1998發表。是 AES 入圍者之一，不過後面沒當選。

Twofish 需要 128-bit block size 以及 128 到 256 bits 的key。 其對於 32-bit CPUs 具有優化。

此演算法目前尚未被成功破解，並且沒有申請任何專利，可免費使用。

> 其演算法定義: [https://www.schneier.com/academic/paperfiles/paper-twofish-paper.pdf](https://www.schneier.com/academic/paperfiles/paper-twofish-paper.pdf)

其運算過程如下圖

![](/assets/Twofishalgo.svg.png)

> [https://en.wikipedia.org/wiki/Twofish](https://en.wikipedia.org/wiki/Twofish)



