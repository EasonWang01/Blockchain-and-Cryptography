# Bitcoin 交易

比特幣主要用途為支付，所以交易是其重要的一環，在本章節中將介紹比特幣交易，並講解交易過程與原理。

以下我們將使用第三方模組`bitcoinjs-lib`來建立交易。

> 如果還沒安裝，需要先使用npm安裝此模組。
>
> ```
> npm install bitcoinjs-lib
> ```

```js
const bitcoin = require('bitcoinjs-lib')

// 可輸入其他私鑰
const privateKey = "L1uyy5qTuGrVXrmrsvHWHgVzW9kKdrp27wBC7Vs6nZDTF2BRUVwy";
const keyPair = bitcoin.ECPair.fromWIF(privateKey);
const txb = new bitcoin.TransactionBuilder()

txb.addInput('aa94ab02c182214f090e99a0d57021caffd0f195a81c24602b1028b130b63e31', 0)
// addInput第一個參數為上一個unspend 的來源TXid
// 第二個參數為當次該unspend在TXid的output中之index，假設一筆交易有兩個output則他們index則分別為0和1，從0開始往上加1。

txb.addOutput('1Gokm82v6DmtwKEB8AiVhm82hyFSsEvBDK', 15000)
// addOutput第一個參數為要轉給哪個地址，第二個參數為金額(satoshi)

txb.sign(0, keyPair)
// 使用私鑰簽發

// 產生交易訊息
console.log(txb.build());

// hex格式的交易訊息
console.log(txb.build().toHex());
```

執行程式後會產生如下：包含了版本訊息、交易鎖定時間、交易輸入與輸出。

![](/assets/螢幕快照 2018-01-30 上午11.43.32.png)

> 此時交易尚未被廣播，在上圖中的最下方Hex字串即為可以用來廣播的交易訊息。

產生2 to 2 的交易 \( 兩個輸入的地址與兩個輸出的地址 \)

```js
const bitcoin = require("bitcoinjs-lib");

// 兩個人的私鑰
const alice = bitcoin.ECPair.fromWIF('L1Knwj9W3qK3qMKdTvmg3VfzUs3ij2LETTFhxza9LfD5dngnoLG1')
const bob = bitcoin.ECPair.fromWIF('KwcN2pT3wnRAurhy7qMczzbkpY5nXMW2ubh696UBc1bcwctTx26z')

const txb = new bitcoin.TransactionBuilder()
// Alice 餘額中前一筆交易的 output，含有 200000 satoshis
txb.addInput('b5bb9d8014a0f9b1d61e21e796d78dccdf1352f23cd32812f4850b878ae4944c', 6) 
// Bob 餘額中前一筆交易的 output，含有 300000 satoshis
txb.addInput('7d865e959b2466918c9863afca942d0fb89d7c9ac0c99bafc3749504ded97730', 0) 

// 發送給另外兩個地址
txb.addOutput('1CUNEBjYrCn2y1SdiUMohaKUi4wpP326Lb', 180000)
txb.addOutput('1JtK9CQw1syfWj1WtFMWomrYdV3W2tWBF9', 170000)
// (in)(200000 + 300000) - (out)(180000 + 170000) = 礦工手續費為：150000

txb.sign(1, bob) // Bob 簽發他第二個input
txb.sign(0, alice) // Alice 簽發他第一個input

console.log(txb.build().toHex())
```

但這時交易還沒有被廣播出去，意思是我們這個簽發動作也可以離線進行，然後把剛才產生的交易訊息複製到其他地方廣播給比特幣網路，稍後下一節將會講解。

再來我們也可以從產生出的交易Hex解碼回原本的格式：

```js
const bitcoin = require('bitcoinjs-lib')
const keyPair = bitcoin.ECPair.fromWIF('L1uyy5qTuGrVXrmrsvHWHgVzW9kKdrp27wBC7Vs6nZDTF2BRUVwy')
const Txb = new bitcoin.TransactionBuilder()

Txb.addInput('aa94ab02c182214f090e99a0d57021caffd0f195a81c24602b1028b130b63e31', 0)
Txb.addOutput('1Gokm82v6DmtwKEB8AiVhm82hyFSsEvBDK', 15000)
Txb.sign(0, keyPair)
TxbHEX = Txb.build().toHex()
console.log(TxbHEX);

const DecodeTxb = bitcoin.Transaction.fromHex(TxbHEX);
console.log(DecodeTxb)
```

### 試試看直接解碼其他已經發生過的交易Hex

1.我們先到以下URL，查看一筆交易的交易Hex內容：

[https://blockchain.info/tx/9021b49d445c719106c95d561b9c3fac7bcb3650db67684a9226cd7fa1e1c1a0?format=hex](https://blockchain.info/tx/9021b49d445c719106c95d561b9c3fac7bcb3650db67684a9226cd7fa1e1c1a0?format=hex)

然後把它貼到下面程式的`transactionHex`變數位置

```js
const bitcoin = require('bitcoinjs-lib')
const transactionHex = "貼上剛網頁上顯示的一串數字"

const transaction = bitcoin.Transaction.fromHex(transactionHex);
console.log(transaction);
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

一個編碼過的交易Hex類似如下

```
01000000018964907116f6245417b9d9aea1d226e46486c5d485d6b947c95c794e032df6120100
00001976a9140043be27e16b93c275413e9b4411f08f2cd8bef088acffffffff0120082f080000
00001976a9140b01599fd09ef602d71827601871e5a1081459f688ac00000000
```

其是由以下格式所串接所組成\( 括號內之數字為其左側數字的10進位格式 \)

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
| :---: | :--- | :--- |
| 版本 | 描述版本號，現在為1 | 4bytes |
| 輸入計數 | 被包含的輸入交易數量 | 1-9 bytes |
| 輸入 | 列出輸入的交易 | 不一定 |
| 輸出計數 | 被包含的輸出交易數量 | 1-9 bytes |
| 輸出 | 列出輸出的交易 | 不一定 |
| 鎖定時間 | 此時間之前交易不能被加入區塊\(註1\) | 4 bytes |

# 廣播交易

比特幣交易可以 Offline 離線產生，產生後再用線上的方式廣播給 Bitcoin 網路，只要把交易訊息的Payload傳送給其它比特幣節點就可以達到廣播的目的。

```js
const bitcoin = require("bitcoinjs-lib");
const key = bitcoin.ECPair.fromWIF("L1Kzcyy88LyckShYdvoLFg1FYpB5ce1JmTYtieHrhkN65GhVoq73");
const txb = new bitcoin.TransactionBuilder();
txb.addInput("d18e7106e5492baf8f3929d2d573d27d89277f3825d3836aa86ea1d843b5158b", 1);
txb.addOutput("12idKQBikRgRuZEbtxXQ4WFYB7Wa3hZzhT", 149000);
txb.sign(0, key);
console.log(txb.build().toHex());
```

接著將以上程式產生出來的Hex格式交易本文，使用如下網站的線上服務廣播到比特幣網路  
[https://blockchain.info/pushtx](https://blockchain.info/pushtx)  
![](/assets/adfw.png)或是使用此網站也可以廣播交易：[https://live.blockcypher.com/btc/pushtx/](https://live.blockcypher.com/btc/pushtx/)

> 兩個網站功能均類似，但blockcypher之回傳訊息較為明確，讀者也可以尋找其他可以廣播交易的工具。

一旦一筆比特幣交易被發送到任意一個連接至比特幣網路的節點，這筆交易將會被該節點驗證。如果交易被驗證為有效，該節點將會將這筆交易傳播到這個節點所連接的其他節點。

每個節點在傳播每一筆交易之前均進行驗證， 如果該節點驗證交易不成功，則不會繼續傳播該交易，礦工會根據每筆交易目前的優先級來決定哪些要先納入下一個區塊中。

優先級的計算方式如下 :

```
(交易金額 * 比特幣存在尚未確認的交易池之時間) / 交易大小

即為

priority = sum(input_value_in_base_units * input_age) / size_in_bytes
```

> 交易池為交易在被節點接收到並且成功驗證，但還沒被納入區塊中的交易所存放的地方。

假設一個交易有兩個輸入， 第一個為 5 btc 擁有 10 個確認\(confirmations\)，另一個為 2 btc 有 3 個確認\(confirmations\), 大小為 500 bytes，其優先級計算為。

```
(500000000 * 10 + 200000000 * 3) / 500 = 11,200,000
```

但後來2017年把上面的優先級機制取消，目前交易納入區塊的優先級單純以手續費為依據。

> Today miners choose which transactions to mine only based on fee-rate
>
> 可參考如下連結：  
> [https://github.com/bitcoin/bitcoin/pull/9602/files\#diff-cd7b305fd4b4280f22ae88960e60398eL298](https://github.com/bitcoin/bitcoin/pull/9602/files#diff-cd7b305fd4b4280f22ae88960e60398eL298)  
> [https://bitcoin.stackexchange.com/questions/54583/why-is-the-transaction-priority-removed](https://bitcoin.stackexchange.com/questions/54583/why-is-the-transaction-priority-removed)

### 礦工節點收到交易後驗證過程如下

```
1.交易的語法和數據結構必須正確。 

2.輸入與輸出列表都不能為空。 

3.交易的bytes大小必須小於MAX_BLOCK_SIZE。 

4.每一個輸出值，以及總量，必須在規定值的範圍內 （大於0且小於2100萬個比特幣）。

5.沒有Hash等於0，N等於-1的輸入（hash = 0,n = -1 為 coinbase transaction，只會發生在挖到區塊時獎勵給礦工的那筆交易）。 

6.nLockTime(指定在交易發生前的鎖定時間)是小於或等於INT_MAX(31 bits)，
  交易大小bytes >= 100，sig opcount <= 2( 意思為 signature 的 opcode 執行不可多於兩個)。 

7.解鎖腳本（scriptSig）只能夠將數字壓入棧中，並且鎖定腳本（scriptPubkey）必須要符合isStandard的格式 （參考下面交易類型部分）。 

8.假設發出的交易Tx hash已經出現在pool中等待納入區塊，或是已經出現在以前的區塊中則一樣拒絕。

9.對於每一個交易輸入，在區塊鏈和目前等待納入的交易池中尋找引用的輸出交易。如果缺少對應的輸出交易，該交易將成為一個孤兒交易( 可能發生於該交易引用的前一筆輸出交易尚未被節點接收到 )。

10.對於每一個輸入，如果引用的輸出交易是一個coinbase輸出，該輸入必須至少獲得COINBASE_MATURITY ( 100 個區塊以上的確認)。 

11.對於每一個輸入，引用的輸出是必須存在的，並且沒有被花費(UTXO)。 

12.使用引用的輸出交易獲得輸入值，並檢查每一個輸入值和總值是否在規定值的範圍內 （大於0且小於2100萬個比特幣）。 

13.如果交易輸入值的總和小於輸出值的總和，也就是餘額小於要花費的金額，交易將被中止。 

14.如果交易手續費太低以至於無法進入下一個新區塊，交易將被拒絕。 

15.驗證 scriptPubKey 符合正確格式
```

如果以上都驗證成功後即可進入納入交易至區塊中的步驟：

```
16.加入交易池中

17.礦工挖到區塊後剛才驗證的交易都會加入該區塊中

18.礦工把自己算出的區塊廣播給其他節點

19.根據新的區塊把剛才納入孤兒交易(orphan transaction)在跑一次以上過程，讓他們可以加入下一個區塊
```

相關文件：[https://en.bitcoin.it/wiki/Protocol\_rules\#.22tx.22\_messages](https://en.bitcoin.it/wiki/Protocol_rules#.22tx.22_messages)  
原始碼 : [https://github.com/bitcoin/bitcoin/blob/3081fb9a31054224759453c3ca400b9076ab8004/src/main.cpp\#L924](https://github.com/bitcoin/bitcoin/blob/3081fb9a31054224759453c3ca400b9076ab8004/src/main.cpp#L924)

# 交易確認\(confirmation\)

> Each additional confirmation is a new block being found and added to the end of the blockchain.
>
> 當交易被納入區塊後，交易的確認數會增加一，而下一個區塊產生後此交易的確認數變為二，之後每有新的區塊產生，交易確認數都會增加一。

1.官方的比特幣用戶端程式在交易經過六個區塊確認之前都會顯示`n/unconfirmed`，用意是為了避免雙重支付\(double spending\)，但根據每個錢包或是交易所定義，可以有不同可確認數，例如`bitfinex交易所`有三個確認後即可確認到帳。

2.礦工挖礦獲得的比特幣收入`coinbase transaction`必須在100個區塊確認後才可用來交易。

> Freshly-mined coins cannot be spent for 100 blocks.
>
> 寫在如下原始碼中[https://github.com/bitcoin/bitcoin/blob/1d9d314573ee48f6f51107265f1cf1fa9e36c998/src/consensus/consensus.h\#L14](https://github.com/bitcoin/bitcoin/blob/1d9d314573ee48f6f51107265f1cf1fa9e36c998/src/consensus/consensus.h#L14)

3.以下網站可用來計算在特定的hashrate運算比例與確認數下可能被成功 hack \( double spend \) 的機率

[https://people.xiph.org/~greg/attack\_success.html](https://people.xiph.org/~greg/attack_success.html)

![](/assets/螢幕快照 2018-01-30 下午2.03.43.png)

> 此網站之上面的輸入框`Proportion of hash-power`  
> 1代表100%，0.1為10%

寫在白皮書的定義 :

![](/assets/螢幕快照 2018-01-20 下午11.28.50.png)

公式與程式：![](/assets/螢幕快照 2018-01-20 下午11.29.02.png)

其他可能使比特幣交易受到竄改的原因，可參考如下連結：  
[https://en.bitcoin.it/wiki/Irreversible\_Transactions](https://en.bitcoin.it/wiki/Irreversible_Transactions)

# UTXO

比特幣地址中的餘額指的是上一筆轉入該地址的交易金額，還沒有被花費掉的部分 \( Unspend output \)。

我們可以到以下網址，查看一個地址的Unspend output，記得在網址最後輸入要查詢的比特幣地址  
[https://blockchain.info/unspent?active=輸入地址](https://blockchain.info/unspent?active=1MkqfKgTp1NZ5eSkTD8aUVZi1VS9myJZHb)

如果該地址還有`Unspend output`則會顯示類似如下：

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

可以看到其中 value 欄位即為該地址的餘額，單位為satoshi

> 0.00000001 bitcoin 為一個 satoshi，此也為bitcoin的最小單位 所以此處 0.00000001 \* 3009040 即為0.0300904BTC  
> ![](/assets/交易餘額.png)

# 交易的Input一定會對應到一個Output

> 以下用Input From 和 Output To 叫好理解

比特幣記錄帳戶餘額流向的方式來自於查看目前尚未花費的金額\( Unspend output \)來自於哪一筆交易的Input，Unspend output 也稱作UTXO （ Unspent Transaction Output）。

Input一定會對應到一個Output，每個Output都會有一個Locking script \( 鎖定腳本 ） 以及每個Input也會有一個 Unlocking script \( 解鎖腳本 \) 用來解鎖Unspend output，解鎖後才可由 Unspend output 傳比特幣給下一個地址變為新地址的 Unspend output。

一筆交易主要包含兩部份：Input 和 Output，Output 有 Locking script 去鎖著 UTXO 不被花費，想要花費的人就需要創建一個 Input，用裡面的 Locking script 去解鎖 Unlocking script，例如今天想要花自己的錢就必須自己去創建一個Input然後用自己的私鑰簽發交易。

![](/assets/en-transaction-propagation.svg)

> 圖片來源：[https://bitcoin.org](https://bitcoin.org)

一筆交易中可以有多個輸入和輸出。  
![](/assets/789.png)

# 交易的ID \(TXID\)

每個比特幣交易都存在一個獨特的 ID 名為 TXID，我們可以從TXID取得那筆交易的相關資訊。

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

發出交易時需要包含一定量的手續費，用來給予礦工，挖到新區塊的礦工將收到該區塊內所有交易的手續費，通常越高的手續費會越早被加入區塊，也就代表可以越早被驗證交易。

以下為比特幣官方錢包用來估算手續費的程式碼：

[https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/wallet/fees.cpp](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/wallet/fees.cpp)

# 交易類型

每筆交易在被節點接收到時會先被確認是不是一個『 standard 』的標準交易，原始碼可查看如下連結：[ https://github.com/bitcoin/bitcoin/blob/5961b23898ee7c0af2626c46d5d70e80136578d3/src/policy/policy.cpp\#L82](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/policy/policy.cpp#L57)

以下將介紹比特幣的五種交易類型:

#### 1.Pay-to-Public-Key-Hash \(P2PKH\)

此為最常見的交易類型。

其Locking script 如下圖 \( 有關Locking script 將於後續 Bitcoin script 章節介紹\)，Locking scrip通常會出現在交易的Output裡面，所以也稱為Output script。另外也稱為scriptPubKey。

> 因為交易是產生一筆Input去解鎖上一個還沒花費的Output，而Output會被Locking script鎖住

```
OP_DUP OP_HASH160 <Public Key Hash(比特幣地址在base58編碼前的樣子)> OP_EQUAL OP_CHECKSIG
```

其Input script如下圖，Unlocking script 通常會出現在交易的Input裡面，所以也稱為Input script。另外也稱scriptSig。

```
<Signature> <Public Key>
```

![](/assets/螢幕快照 2018-01-20 下午11.15.56.png)

可參考下圖實際交易之script

> ![](/assets/32.png)[https://blockchain.info/tx/17a17d2fe6b201f15151cfb6c1c4d6fce254698c5c8a04ee09f92f808abf3325](https://blockchain.info/tx/17a17d2fe6b201f15151cfb6c1c4d6fce254698c5c8a04ee09f92f808abf3325)

#### 2.Pay-to-Public-Key \(P2PK\)

此為較簡單的交易格式，是比較早期的交易型態，相較 P2PKH 省略了Hash步驟，最常在 Coinbase TX 裡面出現，公鑰已經存在Locking script中。

鎖定腳本 \( Locking script \)

```
<Public Key A> OP_CHECKSIG
```

解鎖腳本\( Unlocking script \)

```
<Signature from Private Key A>
```

兩者組合後的交易完整腳本如下：

```
<Signature from Private Key A> <Public Key A> OP_CHECKSIG
```

#### 3.Multi-Signature \(MultiSig，多重簽章交易，需要多個私鑰簽名才可完成簽發\)

可以有 N 把 Public Key 被記錄在上面，而 Unlocking script 解鎖時至少要有 M 把 Private Key 產生的 Signature 才能執行交易，而 M  會小於或等於 N。

```
M-of-N Multi-Signature
```

其 Locking script 類似如下

```
M <Public Key 1> <Public Key 2> ... <Public Key N> N OP_CHECKMULTISIG
```

假設為 3-of-5 Multi-Signature 意思代表為建立交易時會放入五個 Public key，而之後要使用該筆金額時會需要至少三個 Private key簽名才能成功發送交易。

3-of-5 Multi-Signature 的 Locking script類似如下

```
3 <Public Key A> <Public Key B> <Public Key C> <Public Key D> <Public Key E> 5 OP_CHECKMULTISIG
```

> 真實交易可參考: [https://www.blocktrail.com/BTC/tx/6e2c5b6b2a926a7da2415ae55c701e83d6e7bc46bad9bc23b5f9c7ef1ae26998](https://www.blocktrail.com/BTC/tx/6e2c5b6b2a926a7da2415ae55c701e83d6e7bc46bad9bc23b5f9c7ef1ae26998)

![](/assets/螢幕快照 2018-02-01 下午2.59.54.png)

多重簽章交易的Unlocking script如下：

```
OP_0 <Signature B> <Signature C>
```

兩者組合後的交易完整腳本如下：

```
Pubkey script: <m> <A pubkey> <B pubkey> <C pubkey> ... <n> OP_CHECKMULTISIG
Signature script: OP_0 <A sig> <B sig> <C sig>...
```



