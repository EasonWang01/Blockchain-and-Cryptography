```
1. git clone https://github.com/bitcoinjs/bitcoinjs-lib.git

2. cd bitcoinjs-lib 

3. npm install

4. cd test\integration

5. 開啟transaction.js
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

### 3.產生一筆交易並且廣播

```js
var assert = require('assert')
var bitcoin = require('../../')
var dhttp = require('dhttp/200')
var testnet = bitcoin.networks.testnet
var testnetUtils = require('./_testnet')

function rng () {// 一個隨機的base64 hash
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

  // 使用以下在現服務廣播到Bitcoin Testnet network
  dhttp({
    method: 'POST',
    url: 'https://api.ei8ht.com.au:9443/3/pushtx',
    //也可用          url: 'http://tbtc.blockr.io/api/v1/tx/push',
    body: txb.build().toHex()
  }, () => {console.log('success send!')})
})
```

之後會看到如下輸出

```
funding n2n3vHe6BHUwKybSsSSUXK1CtFTafzmR62 w/ 50000
funding mvvrViCXRZD1czZduc4xCixmfG7DpZ7Lkb w/ 70000
```

進入到此網站[https://live.blockcypher.com/btc-testnet](https://live.blockcypher.com/btc-testnet)

然後在右上角輸入地址 即可查看剛才的交易紀錄

