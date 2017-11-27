# 比特幣交易

以下我們用bitcoinjs-lib來建立交易

> 如還沒安裝需先使用npm安裝
>
> ```
> npm install bitcoinjs-lib
> ```

```js
var bitcoin = require('bitcoinjs-lib')
var keyPair = bitcoin.ECPair.fromWIF('L1uyy5qTuGrVXrmrsvHWHgVzW9kKdrp27wBC7Vs6nZDTF2BRUVwy')
var tx = new bitcoin.TransactionBuilder()

tx.addInput('aa94ab02c182214f090e99a0d57021caffd0f195a81c24602b1028b130b63e31', 0) 
//addInput第一個參數為上一個unspend 的來源TXid
//第二個參數為當次該unspend在TXid的output中之index，假設一筆交易有兩個output則他們index則分別為0和1 從0開始排序

tx.addOutput('1Gokm82v6DmtwKEB8AiVhm82hyFSsEvBDK', 15000)
// addOutput第一個參數為要轉給哪個地址，第二個參數為金額(satoshis)

tx.sign(0, keyPair)
//使用私鑰簽發
console.log(tx.build());
```

此時會產生如下

```json
Transaction {
  version: 1,
  locktime: 0,
  ins:
   [ { hash: <Buffer 31 3e b6 30 b1 28 10 2b 60 24 1c a8 95 f1 d0 ff ca 21 70 d5 a0 99 0e 09 4f 21 82 c1 02 ab 94 aa>,
       index: 0,
       script: <Buffer 48 30 45 02 21 00 ae fb cf 84 79 00 b0 1d d3 e3 de be 05 4d 3b 6d 03 d7 15 d5 0a ea 85 25 f5 ea 33 96 f1 68 a1 fb 02 20 13 d1 81 d0 5b 15 b9 01 11 80 ... >,
       sequence: 4294967295,
       witness: [] } ],
  outs:
   [ { script: <Buffer 76 a9 14 ad 61 8c f4 33 3b 3b 24 8f 97 44 e8 e8 1d b2 96 4d 0a e3 97 88 ac>,
       value: 15000 } ] }
```

或是也可以產生2 to 2 的交易\(兩個輸入的地址與兩個輸出的地址\)

```js
var bitcoin = require("bitcoinjs-lib");

var alice = bitcoin.ECPair.fromWIF('L1Knwj9W3qK3qMKdTvmg3VfzUs3ij2LETTFhxza9LfD5dngnoLG1')
var bob = bitcoin.ECPair.fromWIF('KwcN2pT3wnRAurhy7qMczzbkpY5nXMW2ubh696UBc1bcwctTx26z')

var txb = new bitcoin.TransactionBuilder()
txb.addInput('b5bb9d8014a0f9b1d61e21e796d78dccdf1352f23cd32812f4850b878ae4944c', 6) // Alice's previous transaction output, has 200000 satoshis
txb.addInput('7d865e959b2466918c9863afca942d0fb89d7c9ac0c99bafc3749504ded97730', 0) // Bob's previous transaction output, has 300000 satoshis
txb.addOutput('1CUNEBjYrCn2y1SdiUMohaKUi4wpP326Lb', 180000)
txb.addOutput('1JtK9CQw1syfWj1WtFMWomrYdV3W2tWBF9', 170000)
// (in)(200000 + 300000) - (out)(180000 + 150000) = (fee)170000, this is the miner fee

txb.sign(1, bob) // Bob signs his input, which was the second input (1th)
txb.sign(0, alice) // Alice signs her input, which was the first input (0th)

console.log(txb.build().toHex())
```

但這時交易還沒有被廣播出去，意思是我們這個簽發動作也可以離線進行，然後把這個json在複製到其他地方廣播給比特幣網路，稍後下一節將會講解。

我們也可以把剛才code最後一行改為以下，即可看到被編碼為16進位的格式，一般在網路上廣播交易均用此種格式

```js
console.log(tx.build().toHex());
```

再來我們也可以從txHex解碼回原本的格式

```js
var bitcoin = require('bitcoinjs-lib')
var keyPair = bitcoin.ECPair.fromWIF('L1uyy5qTuGrVXrmrsvHWHgVzW9kKdrp27wBC7Vs6nZDTF2BRUVwy')
var tx = new bitcoin.TransactionBuilder()

tx.addInput('aa94ab02c182214f090e99a0d57021caffd0f195a81c24602b1028b130b63e31', 0)
tx.addOutput('1Gokm82v6DmtwKEB8AiVhm82hyFSsEvBDK', 15000)
tx.sign(0, keyPair)
txHEX = tx.build().toHex()
console.log(txHEX);

var tx = bitcoin.Transaction.fromHex(txHEX);
console.log(tx)
```

### 我們也可以試試看直接解碼其他已經發生過的交易的TxHex

1.我們先到此處[https://blockchain.info/tx/9021b49d445c719106c95d561b9c3fac7bcb3650db67684a9226cd7fa1e1c1a0?format=hex](https://blockchain.info/tx/9021b49d445c719106c95d561b9c3fac7bcb3650db67684a9226cd7fa1e1c1a0?format=hex)  
查詢一個交易的txHex，然後把它貼到下面程式的`txHEX`變數位置

```js
txHEX = "0100000002d8c8df6a6fdd2addaf589a83d860f18b44872d13ee6ec3526b2b470d42a96d4d000000008b483045022100b31557e47191936cb14e013fb421b1860b5e4fd5d2bc5ec1938f4ffb1651dc8902202661c2920771fd29dd91cd4100cefb971269836da4914d970d333861819265ba014104c54f8ea9507f31a05ae325616e3024bd9878cb0a5dff780444002d731577be4e2e69c663ff2da922902a4454841aa1754c1b6292ad7d317150308d8cce0ad7abffffffff2ab3fa4f68a512266134085d3260b94d3b6cfd351450cff021c045a69ba120b2000000008b4830450220230110bc99ef311f1f8bda9d0d968bfe5dfa4af171adbef9ef71678d658823bf022100f956d4fcfa0995a578d84e7e913f9bb1cf5b5be1440bcede07bce9cd5b38115d014104c6ec27cffce0823c3fecb162dbd576c88dd7cda0b7b32b0961188a392b488c94ca174d833ee6a9b71c0996620ae71e799fc7c77901db147fa7d97732e49c8226ffffffff02c0175302000000001976a914a3d89c53bb956f08917b44d113c6b2bcbe0c29b788acc01c3d09000000001976a91408338e1d5e26db3fce21b011795b1c3c8a5a5d0788ac00000000"

var tx = bitcoin.Transaction.fromHex(txHEX);
console.log(tx)
```

即可看到原始交易格式

```json
Transaction {
  version: 1,
  locktime: 0,
  ins:
   [ { hash: <Buffer d8 c8 df 6a 6f dd 2a dd af 58 9a 83 d8 60 f1 8b 44 87 2d 13 ee 6e c3 52 6b 2b 47 0d 42 a9 6d 4d>,
       index: 0,
       script: <Buffer 48 30 45 02 21 00 b3 15 57 e4 71 91 93 6c b1 4e 01 3f b4 21 b1 86 0b 5e 4f d5 d2 bc 5e c1 93 8f 4f fb 16 51 dc 89 02 20 26 61 c2 92 07 71 fd 29 dd 91 ... >,
       sequence: 4294967295,
       witness: [] },
     { hash: <Buffer 2a b3 fa 4f 68 a5 12 26 61 34 08 5d 32 60 b9 4d 3b 6c fd 35 14 50 cf f0 21 c0 45 a6 9b a1 20 b2>,
       index: 0,
       script: <Buffer 48 30 45 02 20 23 01 10 bc 99 ef 31 1f 1f 8b da 9d 0d 96 8b fe 5d fa 4a f1 71 ad be f9 ef 71 67 8d 65 88 23 bf 02 21 00 f9 56 d4 fc fa 09 95 a5 78 d8 ... >,
       sequence: 4294967295,
       witness: [] } ],
  outs:
   [ { value: 39000000,
       script: <Buffer 76 a9 14 a3 d8 9c 53 bb 95 6f 08 91 7b 44 d1 13 c6 b2 bc be 0c 29 b7 88 ac> },
     { value: 155000000,
       script: <Buffer 76 a9 14 08 33 8e 1d 5e 26 db 3f ce 21 b0 11 79 5b 1c 3c 8a 5a 5d 07 88 ac> } ] }
```

假設一個編碼過的交易Hex為如下

```
01000000018964907116f6245417b9d9aea1d226e46486c5d485d6b947c95c794e032df612010000001976a9140043be27e16b93c275413e9b4411f08f2cd8bef088acffffffff0120082f08000000001976a9140b01599fd09ef602d71827601871e5a1081459f688ac00000000
```

其是由以下格式所串接所組成

```
version: 01000000
number of inputs: 01 (1)
input 0
     previous txid (little endian): 8964907116f6245417b9d9aea1d226e46486c5d485d6b947c95c794e032df612 (12f62d034e795cc947b9d685d4c58664e426d2a1aed9b9175424f61671906489)
     previous tx output (little endian): 01000000 (1)
     input script (scriptSig) length: 19 (25)
     input script (scriptSig): 76a9140043be27e16b93c275413e9b4411f08f2cd8bef088ac
     nSequence: ffffffff
number of outputs: 01 (1)
output 0
     Satoshis to be spent (little endian): 20082f0800000000 (137300000)
     output script (scriptPubKey) length: 19 (25)
     output script (scriptPubKey): 76a9140b01599fd09ef602d71827601871e5a1081459f688ac
nLockTime: 00000000
```

# 交易結構

| 欄位 | 描述 | 大小 |
| :---: | --- | --- |
| 版本 | 描述版本號，現在為1 | 4bytes |
| 輸入計數 | 被包含的輸入交易數量 | 1-9bytes |
| 輸入 | 列出輸入的交易 | 不一定 |
| 輸出計數 | 被包含的輸出交易數量 | 1-9bytes |
| 輸出 | 列出輸出的交易 | 不一定 |
| 鎖定時間 | 在此時間之前此交易不能被加入區塊\(註1\) | 4bytes |

# 廣播交易

比特幣交易可以是offline產生的，產生後再用線上的方式廣播給bitcoin網路，只要把交易訊息的payload傳送給其中一個比特幣節點就可以達到廣播的目的

```js
var bitcoin = require("bitcoinjs-lib");
var key = bitcoin.ECPair.fromWIF("L1Kzcyy88LyckShYdvoLFg1FYpB5ce1JmTYtieHrhkN65GhVoq73");
var tx = new bitcoin.TransactionBuilder();
tx.addInput("d18e7106e5492baf8f3929d2d573d27d89277f3825d3836aa86ea1d843b5158b", 1);
tx.addOutput("12idKQBikRgRuZEbtxXQ4WFYB7Wa3hZzhT", 149000);
tx.sign(0, key);
console.log(tx.build().toHex());
```

我們可以把剛才產生出來HEX的交易本文使用如下的線上服務廣播到比特幣網路  
[https://blockchain.info/pushtx](https://blockchain.info/pushtx)  
![](/assets/adfw.png)

或是使用[https://live.blockcypher.com/btc/pushtx/](https://live.blockcypher.com/btc/pushtx/)  
\(兩個網站功能均類似，但blockcypher之回傳訊息較為明確\)

一旦一筆比特幣交易被發送到任意一個連接至比特幣網絡的節點，這筆交易將會被該節點驗證。如果交易被驗證有效，該節點將會將這筆交易傳播到這個節點所連接的其他節點

每個節點在傳播每一筆交易之前均進行獨立驗證。 一個異常交易所能到達的節點不會超過一個

礦工會根據每筆交易目前的優先級來決定哪些要先納入下一個區塊中

計算方式如下:

```
交易手續費 * 比特幣存在尚未確認的交易池之時間 

即為

priority = sum(input_value_in_base_units * input_age)/size_in_bytes
```

假設

一個交易有 2 個輸入, 第一個為 5 btc 擁有 10 個確認\(confirmations\), 另一個為 2 btc 有 3 個確認\(confirmations\), 大小為 500bytes, 其優先級計算為

```
(500000000 * 10 + 200000000 * 3) / 500 = 11,200,000
```

但後來2017年把coin age取消，區塊預留的priorty block也移除了，目前交易納入區塊的優先級單純以手續費為依據

> Today miners choose which transactions to mine only based on fee-rate  
> [https://github.com/bitcoin/bitcoin/pull/9602/files\#diff-cd7b305fd4b4280f22ae88960e60398eL298](https://github.com/bitcoin/bitcoin/pull/9602/files#diff-cd7b305fd4b4280f22ae88960e60398eL298)  
> [https://bitcoin.stackexchange.com/questions/54583/why-is-the-transaction-priority-removed](https://bitcoin.stackexchange.com/questions/54583/why-is-the-transaction-priority-removed)

### 礦工節點收到交易後驗證過程如下

```
1.交易的語法和數據結構必須正確。 

2.輸入與輸出列表都不能為空。 

3.交易的bytes大小必須小於MAX_BLOCK_SIZE。 

4.每一個輸出值，以及總量，必須在規定值的範圍內 （大於0且小於2,100萬個比特幣）。

5.沒有Hash等於0，N等於-1的輸入（hash=0, n=-1,即為coinbase transaction）。 

6.nLockTime(指定在交易發生前的鎖定時間)是小於或等於INT_MAX(31 bits)，交易大小bytes >= 100，sig opcount <= 2(關於signature的opcode執行不可多於兩個)。 

7.解鎖腳本（scriptSig）只能夠將數字壓入棧中，並且鎖定腳本（scriptPubkey）必須要符合isStandard的格式 （該格式將會拒絕非標準交易）。 

8.假設發出的交易Tx hash已經出現在pool中等待納入區塊或是已經在以前的區塊中則一樣拒絕。

9.對於每一個交易輸入，在區塊鏈和目前等待納入的交易池中尋找引用的輸出交易。如果缺少對應的輸出交易，該交易將成為一個孤立的交易。

10.對於每一個輸入，如果引用的輸出交易是一個coinbase輸出，該輸入必須至少獲得COINBASE_MATURITY (100區塊以上的確認)。 

11.對於每一個輸入，引用的輸出是必須存在的，並且沒有被花費(UTXO)。 

12.使用引用的輸出交易獲得輸入值，並檢查每一個輸入值和總值是否在規定值的範圍內 （大於0且小於2,100萬個比特幣）。 

13.如果輸入值的總和小於輸出值的總和，交易將被中止。 

14.如果交易手續費太低以至於無法進入下一個新區塊，交易將被拒絕。 

15.驗證 scriptPubKey 符合正確格式

16.加入交易池中

17.礦工挖到區塊後剛才驗證的交易都會加入該區塊中

18.礦工把自己算出的區塊廣播給其他節點

19.根據新的區塊把剛才納入孤兒交易(orphan transaction)在跑一次以上過程，讓他們可以加入下一個區塊
```

[https://en.bitcoin.it/wiki/Protocol\_rules\#.22tx.22\_messages](https://en.bitcoin.it/wiki/Protocol_rules#.22tx.22_messages)  
原始碼:[https://github.com/bitcoin/bitcoin/blob/3081fb9a31054224759453c3ca400b9076ab8004/src/main.cpp\#L924](https://github.com/bitcoin/bitcoin/blob/3081fb9a31054224759453c3ca400b9076ab8004/src/main.cpp#L924)

# 交易確認\(confirmation\)

> 當交易被區塊納入後，其確認數會增加一，之後又在此區塊後產生了一個區塊時此時確認數為二  
> Each additional confirmation is a new block being found and added to the end of the blockchain.

1.原生的比特幣客戶端程式在交易經過六個區塊確認之前都會顯示`n/unconfirmed`，為了避免雙重支付\(double spending\)，但根據每個錢包或是交易所定義，可以有不同可確認樹，例如\`bitfinex\`有三個確認後即可確認到帳

> Freshly-mined coins cannot be spent for 100 blocks

2.礦工挖礦獲得的比特幣收入必須在100個區塊確認後才可交易

> 寫在如下原始碼[https://github.com/bitcoin/bitcoin/blob/1d9d314573ee48f6f51107265f1cf1fa9e36c998/src/consensus/consensus.h\#L14](https://github.com/bitcoin/bitcoin/blob/1d9d314573ee48f6f51107265f1cf1fa9e36c998/src/consensus/consensus.h#L14)

3.在以下網站可用來計算在特定的hashrate運算比例與確認數下可能被成功hack\(double spend\)的機率

[https://people.xiph.org/~greg/attack\_success.html](https://people.xiph.org/~greg/attack_success.html)

> 此網站之上面的輸入框`Proportion of hash-power`  
> 1代表100%,0.1為10%

以下為寫在白皮書的公式  
![](/assets/避免雙重支付公式.png)

有關其他可能被攻擊的方式  
[https://en.bitcoin.it/wiki/Irreversible\_Transactions](https://en.bitcoin.it/wiki/Irreversible_Transactions)

# UTXO

比特幣中的餘額指的是還沒有被花費掉的部分\(unspend output\)

例如我們可以到此網址，查看一個地址的unspend output  
[https://blockchain.info/unspent?active=1MkqfKgTp1NZ5eSkTD8aUVZi1VS9myJZHb](https://blockchain.info/unspent?active=1MkqfKgTp1NZ5eSkTD8aUVZi1VS9myJZHb)

如下

```json
{
  "unspent_outputs":[
    {
     "tx_hash":"6e5704b49d75279c7bfe8067db289a69c5364365f27c6eee27a29795e4ecfd54",
     "tx_hash_big_endian":"54fdece49597a227ee6e7cf2654336c5699a28db6780fe7b9c27759db404576e",
     "tx_index":303475950,
     "tx_output_n": 0,
     "script":"76a914e3ad059f33c82abaa858465b712ff5d874aceb7488ac",
     "value": 3009040,
     "value_hex": "2dea10",
     "confirmations":2
    }
  ]
}
```

可以看到其中value欄位即為該地址的餘額，單位為satoshi

> 0.00000001 bitcoin 為一個 satoshi，此也為bitcoin的最小單位 所以此處 0.00000001 \* 3009040 即為0.0300904BTC  
> ![](/assets/交易餘額.png)

# 交易的Input一定會對應到一個Output

比特幣記錄帳戶餘額流向的方式來自於查看目前花費的金額\(input\)來自於上一筆的output位置

Input一定會對應到一個Output，每個Output都會有一個locking script 以及每個input也會有一個unlocking script  
用來解鎖output，解鎖後才可由上個output傳比特幣給下一個地址變為新地址的unspend output

一筆交易主要包含兩部份，input 和 output，output 有 locking script 去鎖著 UTXO 不被花費，想要花費的人就需要創建一個 input，用裡面的 locking script 去解鎖這個 unlocking script，例如我今天想要花自己的錢就必須自己去創建一個Input然後用我的私鑰簽發

![](/assets/en-transaction-propagation.svg)

> [https://bitcoin.org](https://bitcoin.org)

並且一筆交易中可以來自多個output並產生多個unspend output  
![](/assets/789.png)

# 交易的ID \(TXid\)

每個比特幣交易都存在一個獨特的id名為TXid  
我們可以從TXid取得那筆交易的相關資訊

[https://blockchain.info/rawtx/f60363a12461608b693f2ef89c2bd2bd4821bbdb86b41fa6e8c6732352925ab9](https://blockchain.info/rawtx/f60363a12461608b693f2ef89c2bd2bd4821bbdb86b41fa6e8c6732352925ab9)

```json
{
   "ver":1,
   "inputs":[
      {
         "sequence":4294967295,
         "witness":"",
         "prev_out":{
            "spent":true,
            "tx_index":303467595,
            "type":0,
            "addr":"1J37CY8hcdUXQ1KfBhMCsUVafa8XjDsdCn",
            "value":54718417,
            "n":1,
            "script":"76a914bae025140c454518b8cba0c25d45a6dc87f4b9ce88ac"
         },
         "script":"483045022100dc6db414aa82e684e86c97700e4d0d3c3cd5f971d767f1cc17afc4fbd2ee77190220429d3462ac95d3c42baa82e5f0bfbbfbb998c6aa290a515c702d7b7fe6900af1014104025a1cffe502a80dfa1f543ae14c082aff24d5ecadab160ca904d288eed8e3d8cacef0985f690868df4f416c3890909b4c0c843da153d41382ac20c80170240a"
      }
   ],
   "weight":1024,
   "block_height":495540,
   "relayed_by":"0.0.0.0",
   "out":[
      {
         "spent":false,
         "tx_index":303486321,
         "type":0,
         "addr":"3QXeRSWe7gMHHyfQ9VQCpumWBTJkMXcrdG",
         "value":19200000,
         "n":0,
         "script":"a914fa853359ec49580a6b3f1da326060ce76662c46487"
      },
      {
         "spent":false,
         "tx_index":303486321,
         "type":0,
         "addr":"1J37CY8hcdUXQ1KfBhMCsUVafa8XjDsdCn",
         "value":35337989,
         "n":1,
         "script":"76a914bae025140c454518b8cba0c25d45a6dc87f4b9ce88ac"
      }
   ],
   "lock_time":0,
   "size":256,
   "double_spend":false,
   "time":1511325023,
   "tx_index":303486321,
   "vin_sz":1,
   "hash":"50091e48d3d2edc2d5f2797d9ecf695f61c60f58b227741e8426bb1f8fabb646",
   "vout_sz":2
}
```

# 交易手續費

# 交易種類

#### 1.Pay-to-Public-Key-Hash \(P2PKH\)

此為最常見的交易類型

其Locking script 如下圖\(後續章節會介紹，有關Bitcoin script\)，Locking scrip通常會出現在交易的Input裡面，所以也稱為Input script。另外也稱為scriptPubKey。

```
OP_DUP OP_HASH160 <Public Key Hash也就是比特幣地址> OP_EQUAL OP_CHECKSIG
```

其Output script如下圖，unlocking scrip通常會出現在交易的Output裡面，所以也稱為Output script。另外也稱scriptSig

```
<Signature> <Public Key>
```

#### 2.Pay-to-Public-Key \(P2PK\)

此為更簡單的交易格式，相較 P2PKH 省略了Hash步驟，最常在 coinbase tx 裡面出現，是比較早期的交易型態

#### 3.Multi-Signature \(MultiSig，多重簽章交易，需要多個的私鑰才可完成簽發，最多可包含 15 個 keys\)

#### 4.Data Output \(OP\_RETURN，可以填上自己想填的資料到交易上\)

#### 5.Pay-to-Script-Hash \(P2SH\)

---

註1:[https://en.bitcoin.it/wiki/Timelock](https://en.bitcoin.it/wiki/Timelock)  
以unix timestamp表示，類似於`1511321691`如果填入該欄位的值小於五億，則會把該數字視為區塊高度，意思為在該區塊高度之前不能將交22易加入區塊

註2:交易手續費，約為1000satoshis每KB.每個交易通常至少含有500 bytes.  
[https://bitcoinfees.earn.com/](https://bitcoinfees.earn.com/)

> [https://bitcoinfees.earn.com/](https://bitcoinfees.earn.com/)  
>  \(此網站可看到目前推薦的手續費與尚未確認的交易所含的手續費\)  
> 注意:他是以bytes為單位

註3:build transaction在coinb.in的原始碼  
[https://github.com/OutCast3k/coinbin/blob/217897285e51cbc33bdba3ec275aa3386ebf70b2/js/coin.js\#L793](https://github.com/OutCast3k/coinbin/blob/217897285e51cbc33bdba3ec275aa3386ebf70b2/js/coin.js#L793)

bitcoinjs之transaction相關原始碼  
[https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction.js](https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction.js)  
[https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction\_builder.js](https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction_builder.js)

