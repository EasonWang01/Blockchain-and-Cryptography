# Bitcoin 介紹

比特幣的白皮書在2008年發行，包含了比特幣的核心原理與概念。  
[https://bitcoin.org/bitcoin.pdf](https://bitcoin.org/bitcoin.pdf)![](/assets/螢幕快照 2018-01-31 下午11.22.23.png)以下引言自：[https://bitcoin.org/en/developer-guide\#block-chain](https://bitcoin.org/en/developer-guide#block-chain)

> The block chain provides Bitcoin’s public ledger, an ordered and timestamped record of transactions. This system is used to protect against double spending and modification of previous transaction records.
>
> Each full node in the Bitcoin network independently stores a block chain containing only blocks validated by that node. When several nodes all have the same blocks in their block chain, they are considered to be in consensus. The validation rules these nodes follow to maintain consensus are called consensus rules.

區塊鏈提供了比特幣一個公開帳本，可以記錄所有發生的交易內容以及交易時間，採用區塊鏈的目的為避免交易的雙重花費以及交易紀錄被修改，每個全節點都保存著完整的區塊鏈資料，而許多節點均保存著相同資料的話，即為節點間達成共識。

比特幣由位於世界各地的許多台電腦共同組成`比特幣網路`，每台電腦在完全同步比特幣鏈上的資料後即成為`全節點(Full Node)`，每個全節點都擁有區塊鏈上所有資料，並且可以進行挖礦的動作。

比特幣的礦工們利用挖礦賺取比特幣，挖礦的過程等同於協助驗證交易與區塊，最後組成一個公開帳本`Bitcoin’s public ledger`，人們用比特幣來進行交易。

# 官方網站

#### 1.Bitcoin.org

主要講解比特幣用途以及使用方式，可在該網站下載比特幣官方錢包。

[https://bitcoin.org](https://www.gitbook.com/book/easonwang01/e/edit#)

#### 2.Bitcoin core

Bitcoin Core為比特幣的官方核心軟體，而 bitcoincore.org 網站跟 bitcoin.org 都屬於 Bitcoin 官方，但此網站的內容較為面向開發者。

[https://bitcoincore.org/](https://bitcoincore.org/)

# BIP

BIP是對未來需要改進的比特幣特點，所做的提案。

> BIP stands for Bitcoin Improvement Proposal. A BIP is a design document providing information to the Bitcoin community, or describing a new feature for Bitcoin or its processes or environment. The BIP should provide a concise technical specification of the feature and a rationale for the feature.

可在此處看到詳細內容：[https://github.com/bitcoin/bips](https://github.com/bitcoin/bips)

![](/assets/螢幕快照 2017-11-19 下午1.13.48.png)其中 BIP 又可分為 Standard BIP、Informational BIP 以及 Process BIP，Standard BIP 描述比特幣的核心標準相關內容，而 Informational BIP 通常為一些指南與資訊，Process BIP 為一些正在進行或是提案中的改進建議。

# 比特幣相關狀態查詢

在下面這個網站可以看到比特幣目前的相關狀態，包含目前的平均交易手續費、區塊狀態等等。  
[http://statoshi.info/](http://statoshi.info/)

![](/assets/a.png)

## 比特幣交易訊息與圖表

比特幣交易資訊

[https://blockchain.info/zh-cn/](https://blockchain.info/zh-cn/)

比特幣歷史訊息圖表

[https://blockchain.info/charts](https://blockchain.info/charts)

比特幣地址之餘額排名

[https://bitinfocharts.com/top-100-richest-bitcoin-addresses.html](https://bitinfocharts.com/top-100-richest-bitcoin-addresses.html)

![](/assets/螢幕快照 2018-01-31 下午5.28.58.png)

> 上圖擷取時間為2018/01/31

# 比特幣單位

比特幣分為以下幾種單位，其中最小單位為satoshi。

```
1          BTC   比特幣（Bitcoins，BTC）
0.01       BTC   比特分（Bitcent，cBTC）
0.001      BTC   毫比特（Milli-Bitcoins，mBTC）
0.000001   BTC   微比特（Micro-Bitcoins，μBTC）
0.00000001 BTC   satoshi 為一個為比特幣最小單位
```



