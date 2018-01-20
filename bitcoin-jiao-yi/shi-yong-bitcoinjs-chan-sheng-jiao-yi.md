# 使用BitcoinJS產生相關交易

BitcoinJS為一個MIT授權Open Source的比特幣相關套件。

## 安裝

```js
1. git clone https://github.com/bitcoinjs/bitcoinjs-lib.git

2. cd bitcoinjs-lib 

3. npm install

4. cd test/integration

5. 使用任意編輯器開啟transaction.js
```

然後我們先將transaction.js裡面的code都先刪除，以下將介紹如何產生交易

#### 1.產生一對一之交易\(最基本型態\)

```js
var assert = require('assert')
var bitcoin = require('../../')
var dhttp = require('dhttp/200')
var testnet = bitcoin.networks.testnet
var testnetUtils = require('./_testnet')

// alice的私鑰
var alice = bitcoin.ECPair.fromWIF('L1uyy5qTuGrVXrmrsvHWHgVzW9kKdrp27wBC7Vs6nZDTF2BRUVwy')

//建立交易
var txb = new bitcoin.TransactionBuilder()

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
var assert = require('assert')
var bitcoin = require('../../')
var dhttp = require('dhttp/200')
var testnet = bitcoin.networks.testnet
var testnetUtils = require('./_testnet')

//兩個人的私鑰
var alice = bitcoin.ECPair.fromWIF('L1Knwj9W3qK3qMKdTvmg3VfzUs3ij2LETTFhxza9LfD5dngnoLG1')
var bob = bitcoin.ECPair.fromWIF('KwcN2pT3wnRAurhy7qMczzbkpY5nXMW2ubh696UBc1bcwctTx26z')

var txb = new bitcoin.TransactionBuilder()
//加入兩個Input跟Output
txb.addInput('b5bb9d8014a0f9b1d61e21e796d78dccdf1352f23cd32812f4850b878ae4944c', 6) 
txb.addInput('7d865e959b2466918c9863afca942d0fb89d7c9ac0c99bafc3749504ded97730', 0)
txb.addOutput('1CUNEBjYrCn2y1SdiUMohaKUi4wpP326Lb', 180000)
txb.addOutput('1JtK9CQw1syfWj1WtFMWomrYdV3W2tWBF9', 170000)

//分別簽發，第一個參數為Input在交易的index,第二個參數為私鑰
txb.sign(0, alice) 
txb.sign(1, bob) 

txb.build().toHex();
```

### 3.取得免費測試幣並產生一筆交易

```js
var assert = require('assert')
var bitcoin = require('../../')
var dhttp = require('dhttp/200')
var testnet = bitcoin.networks.testnet
var testnetUtils = require('./_testnet')

function rng() {// 一個隨機的base64 hash
  return Buffer.from('YT8dAtK4d16A3P1z+TpwB2jJ4aFH3g9M1EioIBkLEV4=', 'base64')
}

var alice1 = bitcoin.ECPair.makeRandom({ network: testnet })
var alice2 = bitcoin.ECPair.makeRandom({ network: testnet })
var aliceChange = bitcoin.ECPair.makeRandom({ rng: rng, network: testnet })

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

  var txb = new bitcoin.TransactionBuilder(testnet)
  txb.addInput(unspents[0].txId, unspents[0].vout) // alice1 unspent
  txb.addInput(unspents[1].txId, unspents[1].vout) // alice2 unspent

  //一個為要轉帳的位置，一個為找零的位置，也就是剩餘的錢要找零給自己
  txb.addOutput('mwCwTceJvYV27KXBc3NJZys6CjsgsoeHmf', 8e4) // the actual "spend"
  txb.addOutput(aliceChange.getAddress(), 1e4) // Alice's change

  // Alice signs each input with the respective private keys
  txb.sign(0, alice1)
  txb.sign(1, alice2)

  console.log(txb.build().toHex())
})
```

之後會看到如下輸出

```
funding n2n3vHe6BHUwKybSsSSUXK1CtFTafzmR62 w/ 50000
funding mvvrViCXRZD1czZduc4xCixmfG7DpZ7Lkb w/ 70000
```

進入到此網站[https://live.blockcypher.com/btc-testnet](https://live.blockcypher.com/btc-testnet)\(需稍等一會\)

然後在右上角輸入地址 即可查看剛才的交易紀錄

> 廣播的方式為單純傳一個POST request過去
>
> ![](/assets/766w.png)

# 4.產生OP\_RETURN的交易

```js
var assert = require('assert')
var bitcoin = require('../../')
var dhttp = require('dhttp/200')
var testnet = bitcoin.networks.testnet
var testnetUtils = require('./_testnet')

function rng() {// 一個隨機的base64 hash
  return Buffer.from('YT8dAtK4d16A3P1z+TpwB2jJ4aFH3g9M1EioIBkLEV4=', 'base64')
}

var alice1 = bitcoin.ECPair.makeRandom({ network: testnet })
var alice2 = bitcoin.ECPair.makeRandom({ network: testnet })
var aliceChange = bitcoin.ECPair.makeRandom({ rng: rng, network: testnet })

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
], function (err, unspent) {
  if (err) console.log(err)

  var txb = new bitcoin.TransactionBuilder(testnet)

  // 加入文字
  var data = Buffer.from('bitcoinjs-lib', 'utf8')
  // 加入文字
  var dataScript = bitcoin.script.nullData.output.encode(data)

  txb.addInput(unspent.txId, unspent.vout)
  txb.addOutput(dataScript, 1000)
  txb.addOutput(testnetUtils.RETURN_ADDRESS, 4e4)
  txb.sign(0, keyPair)

  console.log(txb.build().toHex())
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



