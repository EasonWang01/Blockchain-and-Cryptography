# Bitcoin 地址

比特幣地址類似於銀行帳號，可以公開告訴其他人，並且用其來進行收款動作。

> 地址為1開頭為一般比特幣地址，為3開頭的為多重簽章地址，測試鏈地址為m或n開頭。

### 線上產生地址

我們可以用以下網站來進行線上產生地址。

單一簽名：[https://coinb.in/\#newAddress](https://coinb.in/#newAddress)

多重簽名：[https://coinb.in/multisig/](https://coinb.in/multisig/)

> 還有許多網站也提供類似服務，但建議尋找具有離線產生地址的工具較為安全。

### 地址長度

> 每個十六進位字元具有 0.5 bytes

```
私鑰 (Private key):   32 bytes
公鑰 (Public key):    65 bytes
公鑰雜湊 (Public key hash):  20 bytes
```

### 地址開頭字母

下表為各個種類的比特幣地址的開頭。

![](/assets/螢幕快照 2018-01-21 上午11.03.45.png)

> 可參考：[https://en.bitcoin.it/wiki/List\_of\_address\_prefixes](https://en.bitcoin.it/wiki/List_of_address_prefixes)

### 地址種類

比特幣主鏈上常見的地址包含以下種類：

```
1.Pay to Public Key Hash (P2PKH) 單一簽名地址，開頭為1

2.Pay to Script Hash（P2SH）開頭為3 (BIP-16)

3.SegWit Address (P2WSH) 隔離見證地址，開頭為3

4.Time Locked Address 運用OP_CHECKLOCKTIMEVERIFY (OP_HODL) 來創建的地址，開頭為3

5.HD (hierarchical deterministic) 產生出xPub和xPrv，之後再產生地址
```

比特幣地址有以下性質

> 1.由34個英文加數字組成  
> 2.因為採用base58編碼，所以不會含有大寫`O`大寫`I`小寫`i`和數字`0`

### 1.最常用的P2PKH單一簽名地址

單一簽名地址，開頭為1。

下圖為P2PKH比特幣地址的產生流程，利用橢圓曲線來產生相關的公鑰與私鑰，將公鑰進行雜湊，之後進行Base58編碼。![](/assets/螢幕快照 2018-01-30 下午3.33.479s.png)

> [http://en.bitcoinwiki.org/Bitcoin\_address](http://en.bitcoinwiki.org/Bitcoin_address)

下圖為更詳細的地址產生方式，我們將在後續用程式來實作。

![](/assets/0_--6A6E5CY2IT4fmc.png)

> 有些第三方套件在使用橢圓曲線產生的 256 bits 的 public key 時已經在開頭加上了 `0x04` 但有些不會加。
>
> 以下為使用 openssl 產生 secp256k1 私鑰與公鑰的範例 :
>
> ```bash
> openssl ecparam -name secp256k1 -genkey >  priv.pem
> openssl ec -in priv.pem -pubout -outform DER|tail -c 65|xxd -p -c 65
> ```

#### 使用Bitcoind產生P2PKH地址

> 安裝方式於先前章節有介紹。

1.啟動Server

```
./bitcoind -connect=0 -nolisten -datadir="./chaindata"
```

2.產生地址

```
./bitcoin-cli -datadir="./chaindata"  getnewaddress
```

#### 使用Node.js產生P2PKH地址

接下來我們要結合密碼學的幾個加密方法來實作並產生比特幣地址，使用的程式語言是Node.js\(如尚未安裝請參考附錄\)

我們先安裝node.js的base58模組

```
npm install bs58
```

> base58說明：[https://zh.wikipedia.org/wiki/Base58](https://zh.wikipedia.org/wiki/Base58)

範例:

```js
const crypto = require('crypto');
const ecdh = crypto.createECDH('secp256k1');
const bs58 = require('bs58');

const privateKey = crypto.randomBytes(32)
console.log('--------')
console.log('私鑰')
console.log(privateKey.toString('hex')); //私鑰，64位十六進制數 //使用privateKey.toString('hex')即可看到16進位字串
console.log('--------')

// ECDH和ECDSA產生公私鑰的方式都相同
const publickey = ecdh.setPrivateKey(privateKey).getPublicKey('hex')
console.log('公鑰')
console.log(publickey.toString('hex')); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)
console.log('--------')

//把公鑰以sha256加密後再用ripemd160加密，取得publickeyHash
ASCII_text = hex2ASCII(publickey); // 先將publickey轉為ASCII再放入SHA256 這邊可參考: https://bitcoin.stackexchange.com/a/43350
let hash = crypto.createHash('sha256').update(Buffer.from(ASCII_text, "ascii")).digest();

hash = crypto.createHash('ripemd160').update(hash).digest();
console.log('publickeyHash')
console.log(hash.toString('hex'));
console.log('--------')

//在publickeyHash前面加上一个00前缀
let version = new Buffer('00', 'hex');
let checksum = Buffer.concat([version, hash]);
//兩次256雙重加密
checksum = crypto.createHash('sha256').update(checksum).digest();
checksum = crypto.createHash('sha256').update(checksum).digest();

//取前4位得到效驗碼
checksum = checksum.slice(0, 4);
console.log('checksum')
console.log(checksum);
console.log('--------')

//把publickeyHash前面一樣加上00而後面加上剛才算出的checksum
let address = Buffer.concat([version, hash, checksum]);
console.log('編碼前地址')
console.log(address);
console.log('--------')

// 最後使用base58進行編碼
address = bs58.encode(address);
console.log('編碼後的比特幣地址')
console.log(address);
console.log('--------')

// hex 轉為 ASCII 字串
function hex2ASCII(_hex) {
  var hex = _hex.toString();
  var str = '';
  for (var i = 0; i < hex.length; i += 2) {
    str += String.fromCharCode(parseInt(hex.substr(i, 2), 16));
  }
  return str;
}
```

# 2.P2SH\(Pay to Script Hash\)

開頭為3，並且可以選擇發送交易簽名時需要幾個對應的私鑰來做簽名。

因為P2SH交易的Locking script包含OP\_HASH160，跟產生地址的過程類似，所以之後發展為可以將其作Base58編碼，即成為一種地址格式，這也是所謂的P2SH地址。

為一開始發展之多重簽名BIP-11的衍伸，其定義在BIP-16，因為在傳統的Multi Sig需要放入多個Public Key，所以後來決定把具有多個Public Key之script做雜湊，產生一個20-byte 的 Redeem Script Hash。

[https://github.com/bitcoin/bips/blob/master/bip-0016.mediawiki](https://www.gitbook.com/book/easonwang01/e/edit#)

把多個要結合個Public key跟相關Execute code 串接後的字串進行查表 \( [https://en.bitcoin.it/wiki/Script](https://en.bitcoin.it/wiki/Script) \)，轉為Redeem script，之後此Redeem script會進行雜湊，即可算出Redeem Script Hash。

```
m-of-n 多重簽名 之 Redeem script: 

OP_m <Public Key A> <Public Key B> <Public Key C> <Public Key D> <Public Key E> OP_n OP_CHECKMULTISIG
```

#### 使用Bitcoind產生地址

[https://bitcoin.stackexchange.com/questions/6100/how-will-multisig-addresses-work](https://bitcoin.stackexchange.com/questions/6100/how-will-multisig-addresses-work)

1.啟動Server

```
./bitcoind -connect=0 -nolisten -datadir="./chaindata"
```

2.產生三個Address

> 輸入以下指令三次。

```
./bitcoin-cli -datadir="./chaindata"  getnewaddress
```

3.確認目前地址有三個

```
./bitcoin-cli -datadir="./chaindata"  getaddressesbyaccount ""
```

#### ![](/assets/螢幕快照 2018-01-19 下午11.03.39.png)

4.讀取地址的公鑰

> 把validateaddress後面替換為這三個Address，分別取得公鑰

```
./bitcoin-cli -datadir="./chaindata"  validateaddress 1j9x23hbbLDfpExMMHBTQ3CfQ52mqLqLU
```

5.產生出 2 to 3 之 multisig address與redeemScript

```
./bitcoin-cli -datadir="./chaindata"  createmultisig 2 '["039f5d6b7b1c5ca0cffab0fa2b94090591acb01143e0654e2cea14becb6e49d560", "03bdcda873f9399178e8ed6187de1fa34b127b7740299e0d9a9035f8ac480e1994", "03af8b6dc943f593dc7b8690e32ae014257a6dfba5d27e7f433d4d846fde1859e8"]'
```

#### ![](/assets/螢幕快照 2018-01-19 下午11.34.41.png)

> 參考至：[https://bitcoin.stackexchange.com/a/28352](https://bitcoin.stackexchange.com/a/28352)

#### 使用Node.js產生P2SH地址

```js
const crypto = require('crypto');
const bs58 = require('bs58');

// 查表
const OP_2 = "52";
const OP_3 = "53";
const OP_CHECKMULTISIG = "ae";

// 把 65 bytes 推入 stack 如此計算： parseInt("65").toString(16) 即為65轉為16進位 = 41
const pubkeyBytes = "41"

//三個隨機產生的公鑰
const pubkey1 = "045ddb9d95c17d47d0c6d8ab7385cf21cd7d5a411786aeb241f6812d3cf3476200c3f92f6740dd5565ccf249a8e03045931fa5eee736b0bdba94df9db223f809f5";
const pubkey2 = "04ddb9f9da9e667c71a24e341a34c4cbc34f3cd88170e8b4f989b485779c30a55cbea00a5485759aeceff2745df4af6b4e2a58472ce1c8cc9a8bafa1b35d4be672";
const pubkey3 = "0499c36019a9cf95076c27052c424cfce71077cebe23ca22d3b98a7ab54b577e731009feb3aab2068a8e15596db557f2235d46c0e895cdd45f1591b37d4c409869";

redeemScript = OP_2 + pubkeyBytes + pubkey1 + pubkeyBytes + pubkey2 + pubkeyBytes + pubkey3 + OP_3 + OP_CHECKMULTISIG;
console.log('RedeemScript： ')
console.log(redeemScript)
console.log('---------')

ASCII_text = hex2ASCII(redeemScript); // 先將redeemScript轉為ASCII再放入SHA256 這邊可參考: https://bitcoin.stackexchange.com/a/43350
let hash = crypto.createHash('sha256').update(Buffer.from(ASCII_text, "ascii")).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();
console.log('redeemScriptHash')
console.log(hash);
console.log('--------')

//在publickeyHash前面加上一个05前缀 P2SH_BYTE
let version = new Buffer('05', 'hex');
let checksum = Buffer.concat([version, hash]);
//兩次256雙重加密
checksum = crypto.createHash('sha256').update(checksum).digest();
checksum = crypto.createHash('sha256').update(checksum).digest();

//取前4位得到效驗碼
checksum = checksum.slice(0, 4);
console.log('checksum')
console.log(checksum);
console.log('--------')

//把publickeyHash前面一樣加上05而後面加上剛才算出的checksum
let address = Buffer.concat([version, hash, checksum]);
console.log('編碼前地址')
console.log(address);
console.log('--------')

// 最後使用base58進行編碼
address = bs58.encode(address);
console.log('編碼後的多重簽名比特幣地址')
console.log(address);
console.log('--------')

function hex2ASCII(_hex) {
  let hex = _hex.toString();
  let str = '';
  for (let i = 0; i < hex.length; i += 2) {
    str += String.fromCharCode(parseInt(hex.substr(i, 2), 16));
  }
  return str;
}
```

![](/assets/螢幕快照 2018-01-18 下午11.25.11.png)

# 3.SegWit Address

全名為Segregated Witness Address，為隔離見證相容之地址格式。

#### 實作：

相關spec可參考此:

[https://github.com/bitcoin/bips/blob/master/bip-0142.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0142.mediawiki)

[https://bitcoincore.org/en/segwit\_wallet\_dev/\#creation-of-p2sh-p2wsh-address](https://bitcoincore.org/en/segwit_wallet_dev/#creation-of-p2sh-p2wsh-address)

分為兩種：

* ##### P2WPKH
* ##### P2WSH

隔離見證地址，開頭為3，但也有可能看到`bc1q` 與`tb1q`開頭的地址，可參考 BIP-173

[https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki)

> 此種地址產生時，只能接受壓縮版本的公鑰。

E.g.

```
Mainnet P2WPKH: bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4
Testnet P2WPKH: tb1qw508d6qejxtdg4y5r3zarvary0c5xw7kxpjzsx
Mainnet P2WSH: bc1qrp33g0q5c5txsp9arysrx4k6zdkfs4nce4xj0gdcccefvpysxf3qccfmv3
Testnet P2WSH: tb1qrp33g0q5c5txsp9arysrx4k6zdkfs4nce4xj0gdcccefvpysxf3q0sl5k7
```

#### 使用Bitcoind產生地址

> 可使用原先的P2PKH 或 P2SH 來產生，將會構建出P2SH-P2WPKH 或 P2SH-P2WSH。

1.啟動節點（記得加上`-walletprematurewitness`）

```
./bitcoind -connect=0 -nolisten -walletprematurewitness  -datadir="./chaindata"
```

2.產生地址\( 使用原先的 P2PKH 或 P2SH 地址來產生 \)

```
./bitcoin-cli -datadir="./chaindata" addwitnessaddress 1j9x23hbbLDfpExMMHBTQ3CfQ52mqLqLU
```

> 如果填入的地址原先不是從自己錢包產生的會出現如下錯誤。
>
> Public key or redeemscript not known to wallet, or the key is uncompressed.

#### ![](/assets/螢幕快照 2018-01-20 上午10.08.33.png)

#### 使用Node.js產生地址

（Publickey hash 產生後，會在前面接上0x00與0x14然後再做一次sha256與ripemd160然後繼續計算）

```js
const crypto = require('crypto');
const ecdh = crypto.createECDH('secp256k1');
const bs58 = require('bs58');

// 更換其他壓縮版本之私鑰
Compress_PRI_WIF = "L3jzVjVJ4HJLbbWBKqAfwFD7Sh1wYQxcCTuFhG5mCFqcGVmiHMzi"


const bytes = bs58.decode(Compress_PRI_WIF)
const Uncompress_Pri = bytes.toString('hex').slice(2, -10) // 拿掉開頭的80與結尾的01加上八位校驗碼。


const publickey = ecdh.setPrivateKey(Uncompress_Pri, 'hex').getPublicKey('hex')
console.log('--公鑰--')
console.log(publickey); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)


const compressed_publickey = publickey.slice(-1) % 2 === 0
  ? "02" + publickey.slice(2, 66)
  : "03" + publickey.slice(2, 66);
console.log('--Compressed_publickey--')
console.log(compressed_publickey)
console.log('-------------------------')

ASCII_text = hex2ASCII(compressed_publickey);

let hash = crypto.createHash('sha256').update(Buffer.from(ASCII_text, "ascii")).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();


//前面接上0x00與0x14然後再做一次sha256與ripemd160
hash = Buffer.concat([new Buffer('00', 'hex'), new Buffer('14', 'hex'), hash]);
hash = crypto.createHash('sha256').update(hash).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();
//在publickeyHash前面加上一个05前缀
let version = new Buffer('05', 'hex');
let checksum = Buffer.concat([version, hash]);
//兩次256雙重加密
checksum = crypto.createHash('sha256').update(checksum).digest();
checksum = crypto.createHash('sha256').update(checksum).digest();

//取前4位得到效驗碼
checksum = checksum.slice(0, 4);
console.log('checksum')
console.log(checksum);
console.log('--------')

//把publickeyHash前面一樣加上00而後面加上剛才算出的checksum
let address = Buffer.concat([version, hash, checksum]);
console.log('編碼前地址')
console.log(address);
console.log('--------')

// 最後使用base58進行編碼
address = bs58.encode(address);
console.log('編碼後Segwit的比特幣地址')
console.log(address);
console.log('--------')

function hex2ASCII(_hex) {
  let hex = _hex.toString();
  let str = '';
  for (let i = 0; i < hex.length; i += 2) {
    str += String.fromCharCode(parseInt(hex.substr(i, 2), 16));
  }
  return str;
}
```

![](/assets/螢幕快照 2018-01-20 上午9.47.33.png)

