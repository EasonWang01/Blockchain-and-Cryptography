> 在cbc、ofb、cfb、ctr等區塊模式IV長度均為16bytes，gcm的IV則沒有一定要16bytes

#### AES-256-CBC範例

```js
const crypto = require('crypto');

// 因為aes-256要求之key 長度為256bits 也就是32 bytes = 32個ASCII英文字母
// aes-128 要求之key 長度為128bits 也就是16 bytes = 16個英文字母
let key = Buffer.from("abcbbbbbbbbbbbbbabcbbbbbbbbbbbbb", 'utf-8') //or crypto.randomBytes(32);

const IV_LENGTH = 16; 

function encrypt(text) {
    let iv = crypto.randomBytes(IV_LENGTH);
    // 可直接替換為ofb、
cfb、
ctr等模式
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

#### AES-256-GCM範例

> 需要加上cipher.getAuthTag\(\); 與 decipher.setAuthTag\(\);

```js
const crypto = require('crypto');


const mode = 'aes-256-gcm';
// 因為aes-256要求之key 長度為256bits 也就是32 bytes = 32個ASCII英文字母
// aes-128 要求之key 長度為128bits 也就是16 bytes = 16個英文字母
let key = Buffer.from("abcbbbbbbbbbbbbbabcbbbbbbbbbbbbb", 'utf-8') //or crypto.randomBytes(32);

const IV_LENGTH = 12;
let tag;

function encrypt(text) {
    let iv = crypto.randomBytes(IV_LENGTH);

    let cipher = crypto.createCipheriv(mode, new Buffer(key), iv);
    let encrypted = cipher.update(text, 'utf8', 'hex');
    encrypted += cipher.final('hex');
    // 需要加上AuthTag
    tag = cipher.getAuthTag();
    // 將IV附上 在解密時須告知
    return iv.toString('hex') + ':' + encrypted.toString('hex');
}

function decrypt(text) {
    let textParts = text.split(':');
    let iv = new Buffer(textParts.shift(), 'hex');
    let encryptedText = new Buffer(textParts.join(':'), 'hex');
    let decipher = crypto.createDecipheriv(mode, new Buffer(key), iv);
    // 需要加上AuthTag
    decipher.setAuthTag(tag);
    let decrypted = decipher.update(encryptedText, 'hex', 'utf8');
    decrypted += decipher.final('utf8');

    return decrypted.toString();
}

console.log(encrypt("test"));
console.log(decrypt(encrypt("test")))
```

> 其他Node.js的相關範例可參考
>
> https://github.com/nodejs/node-v0.x-archive/blob/master/test/simple/test-crypto-authenticated.js\#L44-L64



