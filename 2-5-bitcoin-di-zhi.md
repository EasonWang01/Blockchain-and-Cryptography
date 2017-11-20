# 2-5 Bitcoin 地址

比特幣地址類似銀行帳號，可以公開告訴其他人，並且用其來進行收款動作

> 地址為1開頭為一般比特幣地址，為3開頭的為多重簽章地址

## 可在以下網站線上產生地址

單一簽名：[https://coinb.in/\#newAddress](https://coinb.in/#newAddress)

多重簽名：[https://coinb.in/multisig/](https://coinb.in/multisig/)

> Private keys are always 32 bytes, and public keys are always 65 bytes \(or 33 bytes for a compressed public key\). Public key hashes are always 20 bytes

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

# 3.SegWit Address

\(P2WSH\) 隔離見證地址，開頭為3

# 4.Time Locked Address

運用OP\_CHECKLOCKTIMEVERIFY \(OP\_HODL\)來創建的地址，開頭為3

可產生經過指定時間後才可用來交易的地址

# 5.HD address \(hierarchical deterministic address\)

參考下圖:

![](/assets/derivation.png)

> [https://bitcoin.stackexchange.com/questions/42559/recovering-a-hd-wallet-from-a-partial-seed-phrase](https://bitcoin.stackexchange.com/questions/42559/recovering-a-hd-wallet-from-a-partial-seed-phrase)

根據[BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 從12個隨機單字產生了128-bit master seed，之後繼續往下階層式的產生出許多地址

但如果master seed 流出去，他底下的所有 key 跟 address 都暴露在風險之中。

## 第一步驟

用 seed 產生  master key，而 seed 的長度 要是 128、256 或是 512 bits  ， 然後將 seed  進行

_HMAC-SHA256  _之後會產生一個長度為 512 bits 的結果， 前 256 bits  為  master private key，後 256 bits 為 master chain code  ， master chain code代表一個 entropy  當 往下產生 child keys 時 會用到 。

流程如下圖

![](/assets/1_ChWUKm31L2WEEpeEB7kzPQ.png)

> [http://chimera.labs.oreilly.com/books/1234000001802/ch04.html\#private\_keys](http://chimera.labs.oreilly.com/books/1234000001802/ch04.html#private_keys)

## 第二步驟

在往下一層走產生 key 的時候需要用名為child key derivation \(CKD\)的function，一樣是做

_HMAC-SHA256_

演算法 ，其需要三個參數

# \#使用Node.js產生比特幣地址

接下來我們要結合密碼學的幾個加密方法來實作並產生比特幣地址，使用的程式語言是Node.js\(如尚未安裝請參考附錄\)

# 1.最常用的P2PKH單一簽名地址

以下將使用node.js實作，我們先安裝node.js的base58模組

```
npm install bs58
```

base58說明：[https://zh.wikipedia.org/wiki/Base58](https://zh.wikipedia.org/wiki/Base58)

```js
var crypto = require('crypto');
var ecdh = crypto.createECDH('secp256k1');
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

# 2.P2SH 多重簽名地址

跟P2PKH產生方式類似，只要把public key換為redeem script即可，redeem script產生方式為下圖：

\(把多個要結合個public key跟相關execute code串接即可\)

> ![](/assets/螢幕快照 2017-11-19 下午3.56.38.png)[http://www.soroushjp.com/2014/12/20/bitcoin-multisig-the-hard-way-understanding-raw-multisignature-bitcoin-transactions/](http://www.soroushjp.com/2014/12/20/bitcoin-multisig-the-hard-way-understanding-raw-multisignature-bitcoin-transactions/)

```js
var crypto = require('crypto');
var bs58 = require('bs58');

// 查表
const OP_2 = "52";
const OP_3 = "53";
const OP_CHECKMULTISIG = "ae";

// Push 65 bytes to stack = parseInt("65").toString(16) 65轉為16進位為41
const pubkeyBytes = "41"
//三個隨機產生的公鑰
const pubkey1 = "045ddb9d95c17d47d0c6d8ab7385cf21cd7d5a411786aeb241f6812d3cf3476200c3f92f6740dd5565ccf249a8e03045931fa5eee736b0bdba94df9db223f809f5";
const pubkey2 = "04ddb9f9da9e667c71a24e341a34c4cbc34f3cd88170e8b4f989b485779c30a55cbea00a5485759aeceff2745df4af6b4e2a58472ce1c8cc9a8bafa1b35d4be672";
const pubkey3 = "0499c36019a9cf95076c27052c424cfce71077cebe23ca22d3b98a7ab54b577e731009feb3aab2068a8e15596db557f2235d46c0e895cdd45f1591b37d4c409869";

redeemScript = OP_2 + pubkeyBytes + pubkey1 + pubkeyBytes + pubkey2 + pubkeyBytes + pubkey3  + OP_3 + OP_CHECKMULTISIG;

//把公鑰以sha256加密後再用ripemd160加密，取得publickeyHash
var hash = crypto.createHash('sha256').update(redeemScript).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();
console.log('redeemScriptHash')
console.log(hash);
console.log('--------')

//在publickeyHash前面加上一个05前缀 P2SH_BYTE
var version = new Buffer('05', 'hex');
var checksum = Buffer.concat([version, hash]);
//兩次256雙重加密
checksum = crypto.createHash('sha256').update(checksum).digest();
checksum = crypto.createHash('sha256').update(checksum).digest();

//取前4位得到效驗碼
checksum = checksum.slice(0, 4);
console.log('checksum')
console.log(checksum);
console.log('--------')

//把publickeyHash前面一樣加上05而後面加上剛才算出的checksum
var address = Buffer.concat([version, hash, checksum]);
console.log('編碼前地址')
console.log(address);
console.log('--------')

// 最後使用base58進行編碼
address = bs58.encode(address);
console.log('編碼後的多重簽名比特幣地址')
console.log(address);
console.log('--------')
```

# 3.SegWit Address \(P2WSH\)

相關spec可參考此:

[https://bitcoincore.org/en/segwit\_wallet\_dev/\#creation-of-p2sh-p2wsh-address](https://bitcoincore.org/en/segwit_wallet_dev/#creation-of-p2sh-p2wsh-address)

隔離見證地址，開頭為3

> ```
> Calculate the RIPEMD160 of the SHA256 of a public key (keyhash).
> The P2SH redeemScript is always 22 bytes. 
> It starts with a OP_0, followed by a canonical push of the keyhash (i.e. 0x0014{20-byte keyhash})
> Same as any other P2SH, the scriptPubKey is OP_HASH160 hash160(redeemScript) OP_EQUAL, and the address is the corresponding P2SH address with prefix 3.
> ```

（以下為地址產生的程式碼，跟一般multisig不同處是publickey hash 產生後，會在前面接上0x00與0x14然後再做一次sha256與ripemd160然後繼續計算）

```js
var crypto = require('crypto');
var ecdh = crypto.createECDH('secp256k1');
var bs58 = require('bs58');


var hash2 = crypto.randomBytes(32)
console.log('--------')
console.log('私鑰')
console.log(hash2); //私鑰，64位十六進制數 //使用hash2.toString('hex')即可看到16進位字串
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

//前面接上0x00與0x14然後再做一次sha256與ripemd160
hash = Buffer.concat([new Buffer('00', 'hex'), new Buffer('14', 'hex'), hash]);
hash = crypto.createHash('sha256').update(hash).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();

//在publickeyHash前面加上一个00前缀
var version = new Buffer('05', 'hex');
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
console.log('編碼後segwit的比特幣地址')
console.log(address);
console.log('--------')
```

> 參考自:[https://github.com/OutCast3k/coinbin/blob/master/js/coin.js\#L175](https://github.com/OutCast3k/coinbin/blob/master/js/coin.js#L175)

# 4.nlockTime address

指定時間後該地址才可進行交易

```
redeemScript = unixTime + OP_CHECKLOCKTIMEVERIFY + OP_DROP + publickey + OP_CHECKSIG;
```

```js
var crypto = require('crypto');
var ecdh = crypto.createECDH('secp256k1');
var bs58 = require('bs58');

// 查表
const OP_2 = "52";
const OP_3 = "53";
const OP_CHECKMULTISIG = "ae";

var hash2 = crypto.randomBytes(32)
console.log('--------')
console.log('私鑰')
console.log(hash2); //私鑰，64位十六進制數 //使用hash2.toString('hex')即可看到16進位字串
console.log('--------')


// ECDH和ECDSA產生公私鑰的方式都相同
var publickey = ecdh.setPrivateKey(hash2,'hex').getPublicKey('hex')
console.log('公鑰')
console.log(publickey); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)
console.log('--------')

var unixTime = Date.now() + 60*60*24*1000*7; // 時間設定在七天後
const OP_CHECKLOCKTIMEVERIFY = "b1";
const OP_DROP = "75";
const OP_CHECKSIG = "ac";

redeemScript = unixTime + OP_CHECKLOCKTIMEVERIFY + OP_DROP + publickey + OP_CHECKSIG;

//把公鑰以sha256加密後再用ripemd160加密，取得publickeyHash
var hash = crypto.createHash('sha256').update(redeemScript).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();
console.log('redeemScriptHash')
console.log(hash);
console.log('--------')

//在publickeyHash前面加上一个05前缀
var version = new Buffer('05', 'hex');
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
console.log('編碼後的nlockTime比特幣地址')
console.log(address);
console.log('--------')
```

# 5.HD address \(hierarchical deterministic address\)

參考下圖:

![](/assets/derivation.png)

> 圖片來源:[https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)

根據[BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 從12個隨機單字產生了128-bit master seed，之後繼續往下階層式的產生出許多地址

## 第一步驟

1.先用 seed 產生一個master key，而 seed 的長度需要是 128、256 或 512 bits

2.然後將 seed 去執行 HMAC-SHA256 加密，之後會產生一個長度為 512 bits 的結果

> 前 256 bits 為 master private key，後 256 bits 為 master chain code ，master chain code 代表 entropy\(熵\)，之後再往下產生 child keys 時會用到。

![](/assets/1_ChWUKm31L2WEEpeEB7kzPQ1.png)圖片來源:[https://github.com/bitcoinbook/bitcoinbook](https://github.com/bitcoinbook/bitcoinbook)

## 第二步驟

往下一層，產生該層各個child private key 的時候，要用到 child key derivation \(CKD\) 這個方法

這個方法一樣是做HMAC-SHA256 加密演算法，但他會需要先輸入三個參數。

```
1.parent public key：拿上一層的 private key 來產生的 public key即是。
2.parent chain code：上一層key的 chain code，為一個entropy(熵)。
3.index：代表著產生下一層的第幾個 key，例如 0 則代表為下一層的第一個key。
```

> 之後會產生跟上層一樣的512bits的key，同樣的，前後256bits分別為 child private key 與 child chain code。

![](/assets/1_ni33v4GKL12m2M4m_5GIQQ.png)圖片來源:[https://github.com/bitcoinbook/bitcoinbook](https://github.com/bitcoinbook/bitcoinbook)

> 由於HMAC-SHA512是Hash function，過程是不可逆的，所以我們不會知道parent是什麼，以及也不會知道自己鄰近的其他child是什麼

# 地址不建議重複使用

[https://en.bitcoin.it/wiki/Address\_reuse](https://en.bitcoin.it/wiki/Address_reuse)

