# 2-5 Bitcoin 地址

比特幣地址類似銀行帳號，可以公開告訴其他人，並且用其來進行收款動作

> 地址為1開頭為一般比特幣地址，為3開頭的為多重簽章地址

## 可在以下網站線上產生地址

單一簽名：[https://coinb.in/\#newAddress](https://coinb.in/#newAddress)

多重簽名：[https://coinb.in/multisig/](https://coinb.in/multisig/)

# 地址種類

```
1.Single Sig Address (Pay to Public Key Hash (P2PKH)) 單一簽名地址，開頭為1

2.Multi Sig Address （P2SH） 多重簽名地址，開頭為3

3.SegWit Address (P2WSH) 隔離見證地址，開頭為3

4.Time Locked Address 運用OP_CHECKLOCKTIMEVERIFY (OP_HODL)來創建的地址，開頭為3

5.HD address (hierarchical deterministic address)產生出xPub和xPrv
```

比特幣地址有以下性質

> 1.由34個英文加數字組成  
> 2.因為採用base58編碼，所以不會含有大寫`O`大寫`I`小寫`i`和數字`0`

## 1.Single Sig Address \(Pay to Public Key Hash \(P2PKH\)\)

單一簽名地址，開頭為1

下圖為P2PKH比特幣地址的產生流程

![](/assets/address1.png)

> [http://en.bitcoinwiki.org/Bitcoin\_address](http://en.bitcoinwiki.org/Bitcoin_address)

## 2.Multi Sig Address （P2SH）

多重簽名地址，開頭為3，產生地址時可以加入一至十五個public key，並且可以選擇發送交易簽名時需要幾個對應的私鑰來做簽名

![](/assets/螢幕快照 2017-11-19 下午3.10.49.png)

> [https://www.blocktrail.com/api/docs](https://www.blocktrail.com/api/docs)

#### 產生方式：

到下面查表

![](/assets/螢幕快照 2017-11-19 下午3.26.17.png)

> [https://en.bitcoin.it/wiki/Script](https://en.bitcoin.it/wiki/Script)

# 

# 使用Node.js產生比特幣地址

接下來我們要結合密碼學的幾個加密方法來實作並產生比特幣地址，使用的程式語言是Node.js\(如尚未安裝請參考附錄\)

#### 1.最常用的P2PKH單一簽名地址

以下將使用node.js實作，我們先安裝node.js的base58模組

```
npm install bs58
```

base58說明：[https://zh.wikipedia.org/wiki/Base58](https://zh.wikipedia.org/wiki/Base58)

```js
var crypto = require('crypto');
var ecdh = crypto.createECDH('secp256k1');//// 最後使用bas58編碼
var bs58 = require('bs58');


var hash2 = crypto.randomBytes(32)
console.log('--------')
console.log('私鑰')
console.log(hash2); //私鑰，64位十六進制數   //使用hash2.toString('hex')即可看到16進位字串
console.log('--------')


// ECDH和ECDSA產生公私鑰的方式都相同
var publickey = ecdh.setPrivateKey(hash2,'hex').getPublicKey('hex')
console.log('公鑰')
console.log(publickey); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)
console.log('--------')

//把公鑰以sha256加密後再用ripemd160加密，取得publickeyHash
var hash = crypto.createHash('sha256').update(publickey).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();
console.log('publickeyHash')
console.log(hash);
console.log('--------')


//在publickeyHash前面加上一个00前缀
var version = new Buffer('00', 'hex');
var checksum = Buffer.concat([version, hash]);
//兩次256雙重加密
checksum = crypto.createHash('sha256').update(checksum).digest();
checksum = crypto.createHash('sha256').update(checksum).digest();

//取前4位得到效驗碼
checksum = checksum.slice(0, 4);
console.log('checksum')
console.log(checksum);
console.log('--------')

//把publickeyHash前面一樣加上00而後面加上剛才算出的checksum
var address = Buffer.concat([version, hash, checksum]);
console.log('編碼前地址')
console.log(address);
console.log('--------')

// 最後使用base58進行編碼
address = bs58.encode(address);
console.log('編碼後的比特幣地址')
console.log(address);
console.log('--------')
```

# 地址不建議重複使用

[https://en.bitcoin.it/wiki/Address\_reuse](https://en.bitcoin.it/wiki/Address_reuse)

