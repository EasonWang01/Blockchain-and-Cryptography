# Triple-DES

因為原本的DES安全性不足，而發展出此演算法，其包含3個DES金鑰，K1，K2和K3，均為56位元

使用K1為金鑰進行DES加密，再用K2為金鑰進行DES「解密」，最後以K3進行DES加密。

Node.js範例

```js
const crypto = require('crypto');

const mode = 'des3'; // 也可用des-ede、des-ede3、des-ecb

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



#### 區塊加密

```js
const crypto = require('crypto');

const mode = 'des-ede3-cbc'; // 可替換為des-ede3-cfb, des-ede3-ofb
const iv = Buffer.from(crypto.randomBytes(8))
const key = Buffer.from(crypto.randomBytes(24))
// 加密
const cipher = crypto.createCipheriv(mode, key, iv);
let encrypted = cipher.update('I_am_plaintext', 'utf8', 'hex');
encrypted += cipher.final('hex');
console.log(encrypted);

// 解密
const decipher = crypto.createDecipheriv(mode, key, iv);
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');
console.log(decrypted);
```



