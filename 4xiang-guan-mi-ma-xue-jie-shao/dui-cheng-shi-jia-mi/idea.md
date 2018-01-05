# IDEA

國際資料加密演算法（英語：International Data Encryption Algorithm，縮寫為 IDEA），為電子郵件加密法**PGP所採用。**

其密鑰長度為128位元。

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



