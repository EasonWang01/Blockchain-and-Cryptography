# 使用BitcoinJS產生相關交易

BitcoinJS為一個MIT License之開放原始碼比特幣相關第三方套件。

## 使用

這裡我們會直接進入其測試資料夾進行修改，並執行。

> 為了避免未來版本更動造成程式無法執行，所以以下連結為筆者fork原專案之程式供讀者具有與本書之相同版本。

```js
1. git clone https://github.com/blockchain-tutorial-by-yicheng/bitcoinjs-lib.git

2. cd bitcoinjs-lib 並且輸入 npm install

3. npm install bitcoinjs-lib  (因為我們在程式裡使用require('bitcoinjs-lib'))，本書版本為3.3.2

4. cd test/integration

5. 使用任意編輯器開啟transaction.js
```

然後我們先將transaction.js裡面的code都先刪除，之後以下範例都直接複製並貼到transaction.js中，並且執行。

> 由於我們會用到以下有關testnet部分，但因為這兩個BitcoinJS沒有打包成相關模組，所以我們使用在`test/integration/transaction.js` 直接改程式之方法執行程式。
>
> ```js
> const testnet = bitcoin.networks.testnet
> const testnetUtils = require('./_testnet')
> ```

本書使用之bitcoinjs-lib版本為**3.3.2，**以下將介紹如何產生交易

#### 1.產生一對一之交易\(最基本型態\)

```js
const bitcoin = require('bitcoinjs-lib');

// alice的私鑰
const alice = bitcoin.ECPair.fromWIF('L1uyy5qTuGrVXrmrsvHWHgVzW9kKdrp27wBC7Vs6nZDTF2BRUVwy')

//建立交易
const txb = new bitcoin.TransactionBuilder()

//addInput 第一個參數為 上一筆unspend的TXid 第二個參數為該unspend在TX中的index位置，假設TX有三個output則index則為0~2
txb.addInput('61d520ccb74288c96bc1a2b20ea1c0d5a704776dd0164a396efec3ea7040349d', 0)

//addOutput第一個參數為要轉帳目標的地址，第二個參數為金額
txb.addOutput('1cMh228HTCiwS8ZsaakH8A8wze1JR5ZsP', 12000)

// 使用私鑰簽名
txb.sign(0, alice);

console.log(txb.build().toHex());
```

### 2.產生二對二之交易

```js
const bitcoin = require('bitcoinjs-lib');

//兩個人的私鑰
const alice = bitcoin.ECPair.fromWIF('L1Knwj9W3qK3qMKdTvmg3VfzUs3ij2LETTFhxza9LfD5dngnoLG1')
const bob = bitcoin.ECPair.fromWIF('KwcN2pT3wnRAurhy7qMczzbkpY5nXMW2ubh696UBc1bcwctTx26z')

const txb = new bitcoin.TransactionBuilder()
//加入兩個Input跟Output
txb.addInput('b5bb9d8014a0f9b1d61e21e796d78dccdf1352f23cd32812f4850b878ae4944c', 6)
txb.addInput('7d865e959b2466918c9863afca942d0fb89d7c9ac0c99bafc3749504ded97730', 0)
txb.addOutput('1CUNEBjYrCn2y1SdiUMohaKUi4wpP326Lb', 180000)
txb.addOutput('1JtK9CQw1syfWj1WtFMWomrYdV3W2tWBF9', 170000)

//分別簽發，第一個參數為Input在交易的index,第二個參數為私鑰
txb.sign(0, alice)
txb.sign(1, bob)

console.log(txb.build().toHex());
```

### 3.取得免費測試幣來產生一筆交易，並且廣播

```js
const crypto = require('crypto');
const bitcoin = require('bitcoinjs-lib');

const testnet = bitcoin.networks.testnet
const testnetUtils = require('./_testnet')

const rng = () => crypto.randomBytes(32);

const alice1 = bitcoin.ECPair.makeRandom({ network: testnet })
const alice2 = bitcoin.ECPair.makeRandom({ network: testnet })
const aliceChange = bitcoin.ECPair.makeRandom({ rng, network: testnet })

// 模擬 Alice 有兩個 unspent outputs
testnetUtils.faucetMany([
  {
    address: alice1.getAddress(),
    value: 5e4
  },
  {
    address: alice2.getAddress(),
    value: 7e4
  }
], function (err, unspents) {
  if (err) console.log(err)

  const txb = new bitcoin.TransactionBuilder(testnet)
  txb.addInput(unspents[0].txId, unspents[0].vout) // alice1 unspent
  txb.addInput(unspents[1].txId, unspents[1].vout) // alice2 unspent

  //一個為要轉帳的位置，一個為找零的位置，也就是剩餘的錢要找零給自己
  txb.addOutput('mwCwTceJvYV27KXBc3NJZys6CjsgsoeHmf', 8e4) // the actual "spend"
  txb.addOutput(aliceChange.getAddress(), 1e4) // Alice's change

  // Alice 用兩把 private keys 分別簽章
  txb.sign(0, alice1)
  txb.sign(1, alice2)

  console.log(txb.build().toHex())
  // 廣播交易
  testnetUtils.transactions.propagate(txb.build().toHex(), function (err) {
    if (err) return console.log(err)
  })
})
```

之後會看到類似如下輸出

```
funding n2n3vHe6BHUwKybSsSSUXK1CtFTafzmR62 w/ 50000
funding mvvrViCXRZD1czZduc4xCixmfG7DpZ7Lkb w/ 70000
```

並且產生交易的Txid

```json
{ 
  success: true,
  txid: '39e72ed626a29435ddf191fd27d1cf9af95fb2c475ed447cf50a748f541f1b0b' 
}
```

可以進入到此網站[https://live.blockcypher.com/btc-testnet](https://live.blockcypher.com/btc-testnet)

然後在右上角輸入剛才的地址或是Txid，即可查看剛才的交易紀錄

### 4.產生OP\_RETURN的交易

```js
const crypto = require('crypto');
const bitcoin = require('bitcoinjs-lib');

const testnet = bitcoin.networks.testnet
const testnetUtils = require('./_testnet')

const rng = () => crypto.randomBytes(32);

var keyPair = bitcoin.ECPair.makeRandom({ network: testnet })
var address = keyPair.getAddress()

testnetUtils.faucet(address, 5e4, function (err, unspent) {
  if (err) return console.log(err)

  var txb = new bitcoin.TransactionBuilder(testnet)
  var data = Buffer.from('bitcoinjs-lib', 'utf8')
  var dataScript = bitcoin.script.nullData.output.encode(data)

  txb.addInput(unspent.txId, unspent.vout)
  txb.addOutput(dataScript, 1000)
  txb.addOutput(testnetUtils.RETURN_ADDRESS, 4e4)
  txb.sign(0, keyPair)

  console.log(txb.build().toHex())
  // 廣播交易
  testnetUtils.transactions.propagate(txb.build().toHex(), function (err) {
    if (err) return console.log(err)
  })
})
```

---

如果產生錯誤，可將integration內的\_testnet.js改為如下

> 主要為更改Endpoint

```js
var async = require('async')
var bitcoin = require('../../')
var Blockchain = require('cb-http-client')
var coinSelect = require('coinselect')
var dhttp = require('dhttp')
var typeforce = require('typeforce')
var types = require('../../src/types')

var BLOCKTRAIL_API_KEY = process.env.BLOCKTRAIL_API_KEY || 'c0bd8155c66e3fb148bb1664adc1e4dacd872548'
var blockchain = new Blockchain('https://api.blocktrail.com/cb/v0.2.1/tBTC', { api_key: BLOCKTRAIL_API_KEY })
var kpNetwork = bitcoin.networks.testnet
var keyPair = bitcoin.ECPair.fromWIF('cQqjeq2rxqwnqwMewJhkNtJDixtX8ctA4bYoWHdxY4xRPVvAEjmk', kpNetwork)
var kpAddress = keyPair.getAddress()
var conflicts = {}

function fundAddress(unspents, outputs, callback) {
  // avoid too-long-mempool-chain
  unspents = unspents.filter(function (x) {
    return x.confirmations > 0 && !conflicts[x.txId + x.vout]
  })

  var result = coinSelect(unspents, outputs, 10)
  if (!result.inputs) return callback(new Error('Faucet empty'))

  var txb = new bitcoin.TransactionBuilder(kpNetwork)
  result.inputs.forEach(function (x) {
    conflicts[x.txId + x.vout] = true
    txb.addInput(x.txId, x.vout)
  })

  result.outputs.forEach(function (x) {
    if (x.address) console.warn('funding ' + x.address + ' w/ ' + x.value)
    txb.addOutput(x.address || kpAddress, x.value)
  })

  result.inputs.forEach(function (_, i) {
    txb.sign(i, keyPair)
  })

  var tx = txb.build()

  blockchain.transactions.propagate(tx.toHex(), function (err) {
    if (err) return callback(err)

    var txId = tx.getId()
    callback(null, outputs.map(function (x, i) {
      return { txId: txId, vout: i, value: x.value }
    }))
  })
}

blockchain.faucetMany = function faucetMany(outputs, callback) {
  blockchain.addresses.unspents(kpAddress, function (err, unspents) {
    if (err) return callback(err)

    typeforce([{
      txId: types.Hex,
      vout: types.UInt32,
      value: types.Satoshi
    }], unspents)

    fundAddress(unspents, outputs, callback)
  })
}

blockchain.faucet = function faucet(address, value, callback) {
  blockchain.faucetMany([{ address: address, value: value }], function (err, unspents) {
    callback(err, unspents && unspents[0])
  })
}

// verify TX was accepted
blockchain.verify = function verify(address, txId, value, done) {
  async.retry(5, function (callback) {
    setTimeout(function () {
      // check that the above transaction included the intended address
      dhttp({
        method: 'get',
        url: 'https://testnet-api.smartbit.com.au/v1/blockchain/tx/' + txId
      }, function (err, result) {
        if (result.body.success === false) return callback(new Error(result.body.error.message))
        if (result.body.transaction.txid !== txId) return callback(new Error('Could not find ' + txId))
        callback()
      })
    }, 400)
  }, done)
}

blockchain.transactions.propagate = function broadcast(txHex, callback) {
  dhttp({
    method: 'POST',
    url: 'https://testnet-api.smartbit.com.au/v1/blockchain/pushtx',
    body: JSON.stringify({ hex: txHex })
  }, function (err, response) {
    if (response.body.success === false) err = new Error(response.body.error.message)
    callback(err, response)
  })
}

blockchain.RETURN_ADDRESS = kpAddress
module.exports = blockchain
```

### 5.產生2-of-4 P2SH \( multisig \)交易

```js
const bitcoin = require('bitcoinjs-lib');
const testnet = bitcoin.networks.testnet
const testnetUtils = require('./_testnet')

// 這邊可改為其他四把WIF key
const keyPairs = [
  '91avARGdfge8E4tZfYLoxeJ5sGBdNJQH4kvjJoQFacbgwmaKkrx',
  '91avARGdfge8E4tZfYLoxeJ5sGBdNJQH4kvjJoQFacbgww7vXtT',
  '91avARGdfge8E4tZfYLoxeJ5sGBdNJQH4kvjJoQFacbgx3cTMqe',
  '91avARGdfge8E4tZfYLoxeJ5sGBdNJQH4kvjJoQFacbgx9rcrL7'
].map(function (wif) { return bitcoin.ECPair.fromWIF(wif, testnet) })
const pubKeys = keyPairs.map(function (x) { return x.getPublicKeyBuffer() })

const redeemScript = bitcoin.script.multisig.output.encode(2, pubKeys)
const scriptPubKey = bitcoin.script.scriptHash.output.encode(bitcoin.crypto.hash160(redeemScript))
const address = bitcoin.address.fromOutputScript(scriptPubKey, testnet)

testnetUtils.faucet(address, 2e4, function (err, unspent) {
  if (err) return console.log(err)

  const txb = new bitcoin.TransactionBuilder(testnet)
  txb.addInput(unspent.txId, unspent.vout)
  txb.addOutput(testnetUtils.RETURN_ADDRESS, 1e4)

  txb.sign(0, keyPairs[0], redeemScript)
  txb.sign(0, keyPairs[2], redeemScript)

  const tx = txb.build()

  // 廣播交易
  testnetUtils.transactions.propagate(tx.toHex(), function (err) {
    if (err) return console.log(err)
  })
})
```

### 6.產生SegWit P2SH-P2WPKH 交易

```js
const bitcoin = require('bitcoinjs-lib');
const testnet = bitcoin.networks.testnet
const testnetUtils = require('./_testnet')

// 可改為其他WIF私鑰
const keyPair = bitcoin.ECPair.fromWIF('cMahea7zqjxrtgAbB7LSGbcQUr1uX1ojuat9jZodMN87JcbXMTcA', testnet)
const pubKey = keyPair.getPublicKeyBuffer()
const pubKeyHash = bitcoin.crypto.hash160(pubKey)

const redeemScript = bitcoin.script.witnessPubKeyHash.output.encode(pubKeyHash)
const redeemScriptHash = bitcoin.crypto.hash160(redeemScript)

const scriptPubKey = bitcoin.script.scriptHash.output.encode(redeemScriptHash)
const address = bitcoin.address.fromOutputScript(scriptPubKey, testnet)

testnetUtils.faucet(address, 5e4, function (err, unspent) {
  if (err) return console.log(err)

  const txb = new bitcoin.TransactionBuilder(testnet)
  txb.addInput(unspent.txId, unspent.vout)
  txb.addOutput(testnetUtils.RETURN_ADDRESS, 4e4)
  txb.sign(0, keyPair, redeemScript, null, unspent.value)

  const tx = txb.build()

  // 廣播交易
  testnetUtils.transactions.propagate(tx.toHex(), function (err) {
    if (err) return console.log(err)
  })
})
```

### 7.產生SegWit 3-of-4 P2SH-P2WSH交易

```js
const bitcoin = require('bitcoinjs-lib');
const testnet = bitcoin.networks.testnet
const testnetUtils = require('./_testnet')

const keyPairs = [
  'cMahea7zqjxrtgAbB7LSGbcQUr1uX1ojuat9jZodMN87JcbXMTcA',
  'cMahea7zqjxrtgAbB7LSGbcQUr1uX1ojuat9jZodMN87K7XCyj5v',
  'cMahea7zqjxrtgAbB7LSGbcQUr1uX1ojuat9jZodMN87KcLPVfXz',
  'cMahea7zqjxrtgAbB7LSGbcQUr1uX1ojuat9jZodMN87L7FgDCKE'
].map(function (wif) { return bitcoin.ECPair.fromWIF(wif, testnet) })
const pubKeys = keyPairs.map(function (x) { return x.getPublicKeyBuffer() })

const witnessScript = bitcoin.script.multisig.output.encode(3, pubKeys)
const redeemScript = bitcoin.script.witnessScriptHash.output.encode(bitcoin.crypto.sha256(witnessScript))
const scriptPubKey = bitcoin.script.scriptHash.output.encode(bitcoin.crypto.hash160(redeemScript))
const address = bitcoin.address.fromOutputScript(scriptPubKey, testnet)

testnetUtils.faucet(address, 6e4, function (err, unspent) {
  if (err) return console.log(err)

  const txb = new bitcoin.TransactionBuilder(testnet)
  txb.addInput(unspent.txId, unspent.vout)
  txb.addOutput(testnetUtils.RETURN_ADDRESS, 4e4)
  txb.sign(0, keyPairs[0], redeemScript, null, unspent.value, witnessScript)
  txb.sign(0, keyPairs[2], redeemScript, null, unspent.value, witnessScript)
  txb.sign(0, keyPairs[3], redeemScript, null, unspent.value, witnessScript)

  const tx = txb.build()

  // 廣播交易
  testnetUtils.transactions.propagate(tx.toHex(), function (err) {
    if (err) return console.log(err)
  })
})
```

> 以上的交易廣播都是廣播到Testnet，廣播到Testnet的交易都可以到以下網站，輸入 txid 或地址查看
>
> [https://live.blockcypher.com/btc-testnet/](https://live.blockcypher.com/btc-testnet/)
>
> 如果出現如下錯誤代表之前廣播過相同交易，但在還沒有被確認之前又再度廣播：
>
> ```json
> error: { code: 'REQ_ERROR', message: '258: txn-mempool-conflict' }
> ```



