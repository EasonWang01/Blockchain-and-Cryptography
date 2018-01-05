



以下為AES-256-CBC範例

```js
const crypto = require('crypto');

// 因為aes-256要求之key 長度為256bits 也就是32 bytes = 32個ASCII英文字母
// aes-128 要求之key 長度為128bits 也就是16 bytes = 16個英文字母
let key = Buffer.from("abcbbbbbbbbbbbbbabcbbbbbbbbbbbbb", 'utf-8') //or crypto.randomBytes(32);

const IV_LENGTH = 16; // For AES, this is always 16

function encrypt(text) {
    let iv = crypto.randomBytes(IV_LENGTH);

    let cipher = crypto.createCipheriv('aes-256-cbc', new Buffer(key), iv);
    let encrypted = cipher.update(text);
    encrypted = cipher.final();

    // 將IV附上 在解密時須告知
    return iv.toString('hex') + ':' + encrypted.toString('hex');
}

function decrypt(text) {
    let textParts = text.split(':');
    let iv = new Buffer(textParts.shift(), 'hex');
    let encryptedText = new Buffer(textParts.join(':'), 'hex');
    let decipher = crypto.createDecipheriv('aes-256-cbc', new Buffer(key), iv);
    let decrypted = decipher.update(encryptedText);

    decrypted = decipher.final();

    return decrypted.toString();
}

console.log(encrypt("test"));
console.log(decrypt(encrypt("test")))
```



