# 以太坊地址

以太坊中有兩種地址，分別為Externally owned account \( EOA \) 與 Contract address\( 合約地址 \)。

Externally owned account \( EOA \) 地址為任何擁有私鑰之人即可用私鑰進行地址來進行發送交易與簽名等動作，而後者 Contract address \( 合約地址 \) 受到智能合約的程式邏輯所管控，也就是該合約的地址。

## Externally owned account \(EOA\)

在Ethereum EOA地址產生過程中，一樣使用橢圓曲線產生公鑰與私鑰，採用secp256k1曲線。其中公鑰使用 uncompressed 模式。

之後把公鑰移除開頭第一個byte，進行SHA3 \( keccak-256 \)雜湊，會得到長度為64的Hex字串，最後取後面40個字，再加上"0x"，即為地址。

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

## Contract address\( 合約地址 \)

當我們部署合約後每個合約會有一個相對應的地址，之後執行合約時都要指定要執行的合約地址。

合約地址的產生方式具有一定邏輯，合約地址會依據部屬合約人的地址以及該部署人之地址總共部屬了幾個合約而定。

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

![](/assets/fefw24.png)對照上圖即可看到右下角之Remix IDE 產生

# 從keyFIle復原出私鑰

當我們用如下方式將私鑰引入到Geth後，他會以一個檔案的型態保存，存在keystore資料夾中

```
personal.importRawKey(私鑰, 密碼)
```

打開檔案後如下格式

```json
{"address":"feddf8db160dcb85f793bfee734352760c4ab96e",
  "crypto":{"cipher":"aes-128-ctr",
  "ciphertext":"39b03a3ba781ac6df72c6492e69c3c140e73f6a04d94ef31e2d9a72449003d9b",
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



