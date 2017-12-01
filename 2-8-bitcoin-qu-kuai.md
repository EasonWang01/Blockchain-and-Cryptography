# 2-8 Bitcoin 區塊

區塊鏈是由許多區塊所組成，每個區塊的資料結構都類似。

## 以下表格為比特幣區塊的資料結構

| **欄位名稱** | **描述** | **欄位所占大小** |
| :--- | :--- | :--- |
| Magic no \(註1\) | 值通常為 0xD9B4BEF9 | 4 bytes |
| Blocksize \(區塊大小\) | 顯示此區塊的大小 | 4 bytes |
| Blockheader\(區塊頭\) | 區塊頭，包含六個部分 | 80 bytes |
| Transaction counter\(交易數量\) | 記錄此區塊包含了幾筆交易 | 1 - 9 bytes |
| transactions | 以Txid表示的交易紀錄列表 | 不一定 |

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
    "tx":[--TXid 陣列--]
{
```

## 區塊頭

上面我們看到在區塊的資料結構裡麵包盒了一個區塊頭的欄位，以下為區塊頭的資料結構

| 欄位 | 描述 | 更新時間 | 大小 \(Bytes\) |
| :--- | :--- | :--- | :--- |
| Version \(版本\) | 目前版本 | 版本更新時 | 4 |
| hashPrevBlock\(前一個區塊的Hash\) | 可用來識別他的父區塊 | 新區塊產生時 | 32 |
| hashMerkleRoot\(參考下一章節Merkel tree\) | 用來快速驗證區塊內的交易 | 新的交易被納入時 | 32 |
| Time\(時間戳記\) | 從 1970-01-01T00:00 UTC開始計算到現在之時間 | 每秒 | 4 |
| Bits\(目標難度\) | 挖出下一個區塊的難度目標 | 每個區塊被挖出時更新 | 4 |
| Nonce\(隨機值\) | 一個32-bit的數字，用於挖礦時納入計算 | 嘗試計算新區塊時，每次會嘗試不同Nonce | 4 |

\(可從上方取得區塊的資料內看到包含區塊頭資料\)

EX:

```json
{
  "ver":1,
  "prev_block":"00000000000007d0f98d9edca880a6c124e25095712df8952e0439ac7409738a",
  "mrkl_root":"935aa0ed2e29a4b81e0c995c39e06995ecce7ddbebb26ed32d550a72e8200bf5",
  "time":1322131230,
  "bits":437129626,
  "nonce":2964215930
}
```

> 區塊頭會被用於挖礦時進行sha256計算，將於之後挖礦章節講解。

# 區塊高度

區塊高度在每次挖出新區塊時會加一，也就是如果目前區塊高度為1000的話，代表目前區塊鏈上已經有1000個區塊。

> 可以看到下圖，為當時最新的四個區塊

![](/assets/39.png)

##### 也可使用以下服務，及會回傳目前區塊高度，也代表著目前區塊鏈中包含著多少區塊。

```
https://blockexplorer.com/api/status?q=getBlockCount
```

## 創世區塊\(genesis block\)

為區塊鏈在一開始創建時，產生的第一個區塊

比特幣的創世區塊：  
[https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f](https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f)

> 可以看到比特幣的創世區塊一開始分配了50BTC\(但無法被使用\)，它的高度以及前一個區塊的Hash都是0，並且在2009/1/3產生![](/assets/833.png)

bitcoin genesis 創建的原始碼  
[https://github.com/bitcoin/bitcoin/blob/3955c3940eff83518c186facfec6f50545b5aab5/src/chainparams.cpp\#L123](https://github.com/bitcoin/bitcoin/blob/3955c3940eff83518c186facfec6f50545b5aab5/src/chainparams.cpp#L123)

##### 

##### 註一:

可在原始碼中看到:

[https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/chainparams.cpp\#L115](https://www.gitbook.com/book/easonwang01/e/edit#)

在電腦科學裡面用到許多數字來代表他現在的版本或ID，可參考[https://en.wikipedia.org/wiki/Magic\_number\_\(programming\](https://www.gitbook.com/book/easonwang01/e/edit#)

##### 



