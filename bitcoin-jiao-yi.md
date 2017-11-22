# UTXO
比特幣中的餘額指的是還沒有被花費掉的部分(unspend output)

例如我們可以到此網址https://blockchain.info/unspent?active=1MkqfKgTp1NZ5eSkTD8aUVZi1VS9myJZHb 查看一個地址的unspend output，如下
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

> 0.00000001 bitcoin 為一個 satoshi，此也為bitcoin的最小單位 所以此處 0.00000001 * 3009040 即為0.0300904BTC
![](/assets/交易餘額.png)



# 交易結構
| 欄位 | 描述 | 大小 |
|:----:|------|------|
|  版本    |   描述版本號，現在為1   |   4bytes   |  
|   輸入計數   |   被包含的輸入交易數量   |   1-9bytes   |   
|   輸入   |   列出輸入的交易   |   不一定   | 
|   輸出計數   |  被包含的輸出交易數量    |   1-9bytes    |   
|   輸出   |   列出輸出的交易   |  不一定  |   
|   鎖定時間   |   在此時間之前此交易不能被加入區塊(註1)   |   4bytes   |   

# 廣播交易

比特幣交易可以是offline產生的，產生後再用線上的方式廣播給bitcoin網路，只要把交易訊息的payload傳送給其中一個比特幣節點就可以達到廣播的目的

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

# 交易種類

```
1.Pay-to-Public-Key-Hash (P2PKH)

2.Pay-to-Public-Key (P2PK) 

3.Multi-Signature (MultiSig，多重簽章交易，需要多個的私鑰才可完成簽發，最多可包含 15 個 keys)

4.Data Output (OP_RETURN，可以填上自己想填的資料到交易上)

5.Pay-to-Script-Hash (P2SH)
```

---

註1:[https://en.bitcoin.it/wiki/Timelock](https://en.bitcoin.it/wiki/Timelock)
以unix timestamp表示，類似於`1511321691`如果填入該欄位的值小於五億，則會把該數字視為區塊高度，意思為在該區塊高度之前不能將交易加入區塊

