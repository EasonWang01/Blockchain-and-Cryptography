# IDEA

國際資料加密演算法（英語：International Data Encryption Algorithm，縮寫為 IDEA），為電子郵件加密法**PGP所採用。**

其密鑰長度為128位元，每個區塊為64位元。

IDEA有八個加密回合，以及最後的0.5回合 \( output transformation \)。





一開始 64 bits，會分成四個16 bits明文，然後進入如下加密過程：

![](/assets/螢幕快照 2018-02-03 上午11.57.55.png)

經過八個階段 \( Round \) 後，最後再經過0.5 Round，得出密文的四個部分。

![](/assets/螢幕快照 2018-02-03 上午11.58.34.png)

https://crypto.stackexchange.com/questions/5076/how-can-i-implement-the-multiplication-modulo-and-addition-modulo-operations



範例:

```js
const crypto = require('crypto');

const mode = 'idea';

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



