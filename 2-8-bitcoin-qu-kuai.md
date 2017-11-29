# 2-8 Bitcoin 區塊

區塊鏈是由許多區塊所組成，每個區塊的資料結構都類似

以下表格為比特幣區塊的資料結構

| **欄位名稱** | **描述** | **欄位所占大小** |
| :--- | :--- | :--- |
| **Magic no \(註1\)** | 值通常為 0xD9B4BEF9 | **4 bytes** |
| **Blocksize \(區塊大小\)** | 顯示此區塊的大小 | **4 bytes** |
| **Blockheader\(**區塊頭**\)** | 區塊頭，包含六個部分 | **80 bytes** |
| **Transaction counter\(交易數量\)** | 記錄此區塊包含了幾筆交易 | **1 - 9 bytes** |
| **transactions** | 以Txid表示的交易紀錄列表 | **不一定** |

我們可以使用

```
https://blockchain.info/rawblock/輸入block hash
```

會回傳類似如下資料

```json
{
    "hash":"0000000000000bae09a7a393a8acded75aa67e46cb81f7acaa5ad94f9eacd103",
    "ver":1,
    "prev_block":"00000000000007d0f98d9edca880a6c124e25095712df8952e0439ac7409738a",
    "mrkl_root":"935aa0ed2e29a4b81e0c995c39e06995ecce7ddbebb26ed32d550a72e8200bf5",
    "time":1322131230,
    "bits":437129626,
    "nonce":2964215930,
    "n_tx":22,
    "size":9195,
    "block_index":818044,
    "main_chain":true,
    "height":154595,
    "received_time":1322131301,
    "relayed_by":"108.60.208.156",
    "tx":[--Array of Transactions--]
{
```





zz



 

 

---

##### 註一:

可在原始碼中看到:

[https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/chainparams.cpp\#L115](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/chainparams.cpp#L115)

在電腦科學裡面用到許多數字來代表他現在的版本或ID，可參考 [https://en.wikipedia.org/wiki/Magic\_number\_\(programming\](https://en.wikipedia.org/wiki/Magic_number_%28programming\)\)

