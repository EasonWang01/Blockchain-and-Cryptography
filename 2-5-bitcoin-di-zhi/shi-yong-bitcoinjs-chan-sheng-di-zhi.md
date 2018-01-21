# 使用BitcoinJS產生地址

#### 安裝

```
npm install bitcoinjs-lib
```

> 本書使用之bitcoinjs-lib版本為**3.3.2**

#### 1.從WIF私鑰來產生地址

```js
const bitcoin = require('bitcoinjs-lib');
const keyPair = bitcoin.ECPair.fromWIF('Kxr9tQED9H44gCmp6HAdmemAzU3n84H3dGkuWTKvE23JgHMW8gct');
const address = keyPair.getAddress();

console.log(address);
```

#### 2.產生2-of-3 P2SH multisig 多重簽章地址

```js
const bitcoin = require('bitcoinjs-lib');

const pubKeys = [
  '026477115981fe981a6918a6297d9803c4dc04f328f22041bedff886bbc2962e01',
  '02c96db2302d19b43d4c69368babace7854cc84eb9e061cde51cfa77ca4a22b8b9',
  '03c6103b3b83e4a24a0e33a4df246ef11772f9992663db0c35759a5e2ebf68d8e9'
].map(function (hex) { return Buffer.from(hex, 'hex') })

const redeemScript = bitcoin.script.multisig.output.encode(2, pubKeys) // 2 of 3
const scriptPubKey = bitcoin.script.scriptHash.output.encode(bitcoin.crypto.hash160(redeemScript))
const address = bitcoin.address.fromOutputScript(scriptPubKey)

console.log(address);
```

#### 3.產生SegWit P2SH 隔離見證地址

```js
const bitcoin = require('bitcoinjs-lib');

const keyPair = bitcoin.ECPair.fromWIF('Kxr9tQED9H44gCmp6HAdmemAzU3n84H3dGkuWTKvE23JgHMW8gct')
const pubKey = keyPair.getPublicKeyBuffer()

const redeemScript = bitcoin.script.witnessPubKeyHash.output.encode(bitcoin.crypto.hash160(pubKey))
const scriptPubKey = bitcoin.script.scriptHash.output.encode(bitcoin.crypto.hash160(redeemScript))
const address = bitcoin.address.fromOutputScript(scriptPubKey)

console.log(address);
```

#### 4.產生SegWit 3-of-4 multisig 多重簽章隔離見證地址

```js
const bitcoin = require('bitcoinjs-lib');

const pubKeys = [
  '026477115981fe981a6918a6297d9803c4dc04f328f22041bedff886bbc2962e01',
  '02c96db2302d19b43d4c69368babace7854cc84eb9e061cde51cfa77ca4a22b8b9',
  '023e4740d0ba639e28963f3476157b7cf2fb7c6fdf4254f97099cf8670b505ea59',
  '03c6103b3b83e4a24a0e33a4df246ef11772f9992663db0c35759a5e2ebf68d8e9'
].map(function (hex) { return Buffer.from(hex, 'hex') })

const witnessScript = bitcoin.script.multisig.output.encode(3, pubKeys) // 3 of 4
const scriptPubKey = bitcoin.script.witnessScriptHash.output.encode(bitcoin.crypto.sha256(witnessScript))
const address = bitcoin.address.fromOutputScript(scriptPubKey)

console.log(address);
```

#### 5.產生SegWit 2-of-2 P2SH multisig 多重簽章隔離見證地址

```js
const bitcoin = require('bitcoinjs-lib');

const pubKeys = [
  '026477115981fe981a6918a6297d9803c4dc04f328f22041bedff886bbc2962e01',
  '02c96db2302d19b43d4c69368babace7854cc84eb9e061cde51cfa77ca4a22b8b9'
].map(function (hex) { return Buffer.from(hex, 'hex') })

const witnessScript = bitcoin.script.multisig.output.encode(2, pubKeys) // 2 of 2
const redeemScript = bitcoin.script.witnessScriptHash.output.encode(bitcoin.crypto.sha256(witnessScript))
const scriptPubKey = bitcoin.script.scriptHash.output.encode(bitcoin.crypto.hash160(redeemScript))
const address = bitcoin.address.fromOutputScript(scriptPubKey)

console.log(address);
```



