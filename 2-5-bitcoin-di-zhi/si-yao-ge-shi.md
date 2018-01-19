# 私鑰格式

比特幣的私鑰長度為 256 bits\(32 bytes\) 在十六進位為 64 個字。

任何在以下範圍中的私鑰都可以是合法的。

```
0x1 到 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4140
```

# WIF\(Wallet import format\)

一般私鑰跟WIF格式私鑰可以互相轉換，其優點在於長度稍微縮短，以及加入了checksum

可參考以下網站在線轉換[http://gobittest.appspot.com/PrivateKey](http://gobittest.appspot.com/PrivateKey)

## 一般私鑰轉為WIF格式

```js
var crypto = require('crypto');
var bs58 = require('bs58');

// 隨機的私鑰
const privateKey = "0C28FCA386C7A227600B2FE50B7CAE11EC86D3BF1FBE471BE89827E19D72AA1D";

// 前面加上0x80
var add0x80_front = "80" + privateKey;

// 使用double sha256   後面.update使用hex原因是nodejs預設v6版本之後預設是utf-8
// https://stackoverflow.com/questions/37213211/why-crypto-createhash-returns-different-output-in-new-version
const sha256Value1 = crypto.createHash('sha256').update(add0x80_front, 'hex').digest();
const sha256Value2 = crypto.createHash('sha256').update(sha256Value1, 'hex').digest();

// 取前八位當作checksum
const first4byte = sha256Value2.toString('hex').substring(0,8);

// 串接
const concatString = add0x80_front + first4byte; 
WIF_PrivateKey = bs58.encode(new Buffer(concatString, 'hex'));
console.log(WIF_PrivateKey)
```

## WIF格式私鑰轉換為原本私鑰格式

```js
var crypto = require('crypto');
var bs58 = require('bs58');

// WIF格式之私鑰
const WIF_privateKey = "5HueCGU8rMjxEXxiPuD5BDku4MkFqeZyd4dZ1jvhTVqvbTLvyTJ";

// base58 decode
const decodeWIF = bs58.decode(WIF_privateKey);

// 把最後四位checksum丟棄
const dropLast4 = decodeWIF.slice(0,33);

// 把第一個byte丟棄
const final = decodeWIF.slice(1, dropLast4.length);
console.log(final)
```

### WIF­ compressed格式

私鑰其實沒有壓縮格式，只有公鑰可以壓縮，而WIF­ compressed的意思為代表其為壓縮格式公鑰的私鑰，如果該私鑰是對應壓縮格式的公鑰則會在私鑰最後加上0x01。

> 壓縮格式的WIF開頭為**K或L，沒壓縮WIF私鑰為5開頭。**
>
> 因為橢圓曲線知道X後即可求得Y，所以可以從原本65bytes的prefix+X+Y，壓縮為33bytes，可參考後面ECDSA章節。

以下分別為一般的私鑰、WIF格式的私鑰、WIF壓縮格式的私鑰。

| Uncompressed private key | 4F63BA6514E7EA4D2F561D96035729D93EDA4678F148A0CF8E8D77724B18E0B9 |
| :--- | :--- |
| Compressed private key | 4F63BA6514E7EA4D2F561D96035729D93EDA4678F148A0CF8E8D77724B18E0B901 |
| Uncompressed WIF | 5JRFUsVCaGpZDhkznzDCKiD28rJEhNrhXLz3rGKwMDU1fhJUHE6 |
| Compressed WIF | Kyt2v1qU5rpuD4uC5yWpyat41eb5TDUMrwCoaSeatE2DwVA7MMfG |

#### 壓縮的WIP私鑰轉為為壓縮的私鑰。

```js
// Compressed WIF Private Key
const compress_WIF_Pri = 'Kyt2v1qU5rpuD4uC5yWpyat41eb5TDUMrwCoaSeatE2DwVA7MMfG'
const bytes = bs58.decode(compress_WIF_Pri)
const Uncompress_Pri = bytes.toString('hex').slice(2, -10) // 拿掉開頭的80與結尾的01加上八位校驗碼。

// Uncompressed private key
console.log(Uncompress_Pri)
```

#### 從WIF私鑰轉為比特幣地址

```js
const bitcoin = require('bitcoinjs-lib')
const keyPair = bitcoin.ECPair.fromWIF('5JdT9kXDHJi3zzyJfYhvpWDAQrk6y7GPCkQiQfdxk5aV4jrq12A') // 輸入WIF格式私鑰
const address = keyPair.getAddress()
console.log(address)
```

# Mini Private Key

通常用在儲存空間較小的地方，例如實體Bitcoin硬幣或是1D barcodes與25x25之QR Code等等。

開頭為S，產生出來的mini key會在最後加上`?`號然後進行sha256，確認前兩個byte是否為00，如果是才為合法的Mini Key

> 以下使用Python程式為範例



> 參考至:[https://en.bitcoin.it/wiki/Mini\_private\_key\_format](https://en.bitcoin.it/wiki/Mini_private_key_format)

---

> 可使用此網站驗證地址與私鑰
>
> [http://gobittest.appspot.com/](http://gobittest.appspot.com/)



