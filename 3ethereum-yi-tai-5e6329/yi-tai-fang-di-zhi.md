# Ethereum 地址

以太坊中有兩種地址，分別為Externally owned account \( EOA \) 與 Contract address\( 合約地址 \)。

Externally owned account \( EOA \) 地址為任何擁有私鑰之人即可用私鑰進行地址來進行發送交易與簽名等動作，而後者 Contract address \( 合約地址 \) 受到智能合約的程式邏輯所管控，也就是該合約部署後的地址。

## Externally owned account \( EOA \)

在Ethereum EOA 地址產生過程中，一樣使用橢圓曲線產生公鑰與私鑰，採用secp256k1曲線。其中公鑰使用 uncompressed 模式。

之後把公鑰移除開頭第一個byte，進行SHA3 \( keccak-256 \)雜湊，會得到長度為64的Hex字串，最後取後面40個字，再加上`0x`，即為地址。

使用原生模組搭配SHA3模組產生地址

```js
const crypto = require('crypto');
const ecdh = crypto.createECDH('secp256k1');
const SHA3 = require('keccakjs')

const random_hash = crypto.randomBytes(32)
console.log('-------------------------------------')
console.log('私鑰Private Key: ')
console.log(random_hash.toString('hex')); //私鑰，32bytes 十六進制數
console.log('-------------------------------------')

// 產生公鑰
const publickey = ecdh.setPrivateKey(random_hash).getPublicKey('hex')
console.log('公鑰Public Key: ')
console.log(publickey); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)
// 產生地址
let pubKeyBuf = new Buffer(publickey, 'hex')
let h = new SHA3(256)
h.update(pubKeyBuf.slice(1)) // 把Buffer前第一個部分移除 130 => 128
let address = h.digest('hex').slice(-40) // 取後面四十個字

console.log('-------------------------------------')
console.log('地址Address: ')
console.log(`0x${address}`)
```

使用第三方模組 \( ethereumjs-wallet \) 產生地址:

```js
const crypto = require('crypto')
const eth = require('ethereumjs-wallet')
const random_hash = crypto.randomBytes(32)
const instance = eth.fromPrivateKey(random_hash)

console.log('private key: ')
console.log(instance.getPrivateKey().toString('hex'))
console.log('-------------------------------------')
console.log('public key: ')
console.log(instance.getPublicKey().toString('hex'))
console.log('-------------------------------------')
console.log('Address: ')
console.log(`0x${instance.getAddress().toString('hex')}`)
```

其他語言可參考:

[https://gist.github.com/lgn21st/1bd2db7eb30b55e17d07a8bc637c2f87](https://gist.github.com/lgn21st/1bd2db7eb30b55e17d07a8bc637c2f87)

## Contract address \( 合約地址 \)

當我們部署智能合約後，每個合約會有一個相對應的地址，之後執行合約時都要指定要執行的合約地址。

合約地址的產生方式具有一定邏輯，合約地址會依據部屬合約人的地址以及該部署人之地址先前部屬過幾個合約而定。

規則如下 :

固定格式為

```
d6 + 94 + 部屬人之地址 + nonce
```

如果該帳號是第一次部屬合約則 nonce 為80，第二次部屬合約 nonce 為01之後則繼續往上03, 04...

> 如果是在mainnet則 nonce 從01開始
>
> [https://github.com/ethereum/EIPs/blob/master/EIPS/eip-161.md\#specification](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-161.md#specification)

範例 :

```js
const SHA3 = require('keccakjs')

function getContract_Address(address, num) {
  let nonce;
  if(num === 0) {
    nonce = 80;
  } else if (num < 10) {
    nonce = `0${num}`;
  }

  let h = new SHA3(256).update(Buffer.from("d6" + "94" + address + nonce, 'hex')).digest('hex');
  let contractAddress = h.slice(-40) // 取後面四十個字
  console.log('-------------------------------------')
  console.log('Contract Address: ')
  console.log(`0x${contractAddress}`)
}

getContract_Address("d7c86c344ecbd9f166b053a32cd6cd34dda1b8af", 0)
getContract_Address("d7c86c344ecbd9f166b053a32cd6cd34dda1b8af", 1)
getContract_Address("d7c86c344ecbd9f166b053a32cd6cd34dda1b8af", 2)
```

![](/assets/fefw24.png)看從上圖看到右下角之Remix IDE 部署後的合約地址與我們用程式計算出的地址相同。

# 從 keyFile 復原出私鑰

當我們用如下方式將私鑰引入到Geth後，他會以一個檔案的型態保存，存在keystore資料夾中

```
personal.importRawKey(私鑰, 密碼)
```

打開檔案後如下格式

```json
{"address":"feddf8db160dcb85f793bfee734352760c4ab96e",
  "crypto":{"cipher":"aes-128-ctr",
  "ciphertext":"19b03a3ba781ac6df72c6492e69c3c140e73f6a04d94ef31e2d9a72449003d9b",
  "cipherparams":{"iv":"d2faaaa483443d97c576f8b3b012c617"},
  "kdf":"scrypt",
  "kdfparams":{
    "dklen":32,
    "n":262144,
    "p":1,"r":8,
    "salt":"1e45349ad4c7db93cb75564563d4792973fc6e8d98db0ffff8e0f9a93004ccd5"},
  "mac":"22411b5dfae71337dfd7b5589758a85f22b33411a227f036e9ab36adfc47ba87"},
  "id":"2cd506ae-d0f2-48e9-b33b-6c57eadea56e","version":3
}
```

我們可以使用以下程式將其還原為私鑰：

需要安裝第三方模組

```
npm install keythereum
```

```js
const keyth = require('keythereum');
const fs = require('fs');

let password = '';
// 讀取keyFile檔案
key = fs.readFileSync('./keyfile.062746081Z--feddf8db160dcb85f793bfee734352760c4ab96e'); 
parsed_key = JSON.parse(key.toString());

const privateKey = keyth.recover(password, parsed_key);

console.log(privateKey.toString('hex'));
// 還原出私鑰
```

# ICAP \( Inter exchange Client Address Protocol \)

[https://github.com/ethereum/wiki/wiki/ICAP:-Inter-exchange-Client-Address-Protocol](https://github.com/ethereum/wiki/wiki/ICAP:-Inter-exchange-Client-Address-Protocol)

以太坊地址的另一種格式名為ICAP，與國際間銀行常用的IBAN格式相容，但目前支援此種地址的錢包較少。

在Ethereum中， IBAN 的country code為`XE` 來源為 Ethereum 開頭的字母 `E` ，而 `X`  意思為 `extended`  ，有關IBAN的詳細說明可參考 \( 註1 \) 的網址，簡言之，IBAN是轉帳時用來辨識銀行的號碼，其中包含以下資訊：帳號、銀行與分行名稱、所在國家號碼。IBAN一般來說不能超過34個字。

```
Country code: 國家號碼 (ISO 3166-1 alpha-2);
Error-detection code: 產生Checksum (ISO/IEC 7064:2003);
The basic bank account number (BBAN): 包含 機構、分行、客戶帳號等資訊。
```

Ethereum的ICAP地址由以下三個部分所組合：

```
1.XE 
2.兩字母的 checksum
3.三種可能類型的 account identifier
```

## BBAN

另一個名詞為BBAN，即為IBAN中的第三個部分，因為沒有統一格式，所以後來才進行擴充，誕生了IBAN。

有關三種可能類型的 account identifier 包含以下 Direct、Basic、Indirect：

> 均會以base-36進行編碼

#### 1.Direct

由於編碼前地址必須小於 155 bits，所以通常會轉成此種類型地址的原始以太幣地址都會是0x00開頭，此種類型可以完全與其他IBAN格式相容，編碼後共30個字母 \( 扣除開頭兩位XE與兩位Checksum\)。

e.g.

```
00c5496aee77c1ba1f0854206a26dda82a81d6d8 
對應到
XE7338O073KYGTWWZN0F2WZ0R8PX5ZPPZS
```

#### 2.Basic

由於編碼前地址必須小於 161 bits ，所以如果開頭不是0x00的原始地址，都只能使用此種格式，編碼後共31個字母 \( 扣除開頭兩位XE與兩位Checksum\)，其無法與其他類型IBAN相容。

#### 3.Indirect

包含16個字母，由以下三個欄位組成。

```
Asset identifier， 3個字
Institution identifier， 4個字
Institution client identifier， 9個字
```

#### 實作：

接著我們可以使用此模組來產生ICAP地址：

[https://github.com/ethereumjs/ethereumjs-icap](https://github.com/ethereumjs/ethereumjs-icap)

```
npm install ethereumjs-icap
```

```js
const ICAP = require('ethereumjs-icap');

// 以下地址第一個因為大於155 bits，所以只能編碼為Basic的ICAP，第三個參數要為True ( 告知轉為Basic格式，如果沒加會產生inValid錯誤 ) 。
// 第二個參數是顯示時是否以space delimited顯示，e.g. XE A9 DD
console.log(ICAP.fromAddress('0x1e44c6c75BCEB565AD4A58750f4BC3571365D8C7', false, true));
console.log(ICAP.fromAddress('0x0844c6c75BCEB565AD4A58750f4BC3571365D8C7')); // 在此地址中將第一個hex改為0，第二個hex改為 < 9，即可滿足bit限制，轉為Direct格式
console.log(ICAP.fromAddress('0x00c5496aee77c1ba1f0854206a26dda82a81d6d8'));

// 從ICAP轉為原本格式的地址
console.log(ICAP.toAddress('XE363JAAEP6QZKWEDX2VC4A4L4ZLIB396MF'));
console.log(ICAP.toAddress('XE7338O073KYGTWWZN0F2WZ0R8PX5ZPPZS'));
```

# Mixed-case checksum address encoding

[https://github.com/Ethereum/EIPs/blob/master/EIPS/eip-55.md](https://github.com/Ethereum/EIPs/blob/master/EIPS/eip-55.md)

定義在EIP55中，名為Mixed-case checksum address encoding。

我們可以發現在把私鑰引入類似Metamask錢包後，其地址會包含大寫與小寫英文，但與全部小寫英文的地址都是對應到同一把私鑰，Mixed-case地址在地址的好處是可以做到checksum，因為Ethereum的地址是不區分大小寫的 （case-insensitive）。

如果有了Mixed-case的地址與原本全部是小寫的地址，之後在輸入轉帳地址時可以把Mixed-case的地址先轉為小寫然後做SHA3 Checksum並跟原本全部是小寫的地址之SHA3 Checksum比較，即可知道有沒有哪些字母拼錯或輸入錯誤。

可以用一下程式轉換：

```js
const SHA3 = require('keccakjs')

function toChecksumAddress (address) {
  address = address.toLowerCase().replace('0x', '')
  let h = new SHA3(256);
  let hash = h.update(address).digest('hex')
  let ret = '0x'

  for (let i = 0; i < address.length; i++) {
    if (parseInt(hash[i], 16) >= 8) { // Hash後的字母 >= 8 就轉為大寫
      ret += address[i].toUpperCase()
    } else {
      ret += address[i]
    }
  }

  return ret
}

// 轉換一個全部都小寫的地址
console.log(toChecksumAddress('0xa4db45dd29fcc0a01937f2230eb189ba12e23abd'))

// 回傳：0xa4Db45dd29fCc0A01937f2230Eb189ba12E23AbD
```

---

註1:

IBAN \( **International Bank Account Number** \)：

[https://www.iban.com/whatis.html](https://www.iban.com/whatis.html)

