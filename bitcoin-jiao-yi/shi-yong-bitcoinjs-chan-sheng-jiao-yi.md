```
1. git clone https://github.com/bitcoinjs/bitcoinjs-lib.git

2. npm install

3. cd test\integration

4. 開啟transaction.js
```

然後我們先將transaction.js裡面的code都先刪除，以下將介紹如何產生交易



1. 產生一對一之交易\(最基本型態\)

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



