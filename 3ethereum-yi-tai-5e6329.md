# 以太坊（Ethereum）簡介

以太坊是一個開源的區塊鏈專案，其交易貨幣稱為以太幣\(ETH\)，和比特幣一樣具有挖礦機制，但其更具特色的是具有智能合約系統，開發者可以撰寫智能合約然後給以太坊的虛擬機\(EVM\)進行編譯，並部署到區塊鏈上，部署後的合約可以被其他人存取，並執行合約上對應的功能，每個被部署到以太坊區塊鏈的智能合約都具有一個地址。

以太坊白皮書：[https://github.com/ethereum/wiki/wiki/White-Paper](https://github.com/ethereum/wiki/wiki/White-Paper)

以太坊黃皮書：[https://ethereum.github.io/yellowpaper/paper.pdf](https://ethereum.github.io/yellowpaper/paper.pdf)

以太坊由 Vitalik Buterin 所發表，目的是作為一個區塊鏈平台，讓大家可以在上面撰寫智能合約並運行。以太坊也與比特幣一樣有交易與挖礦機制，而下面我們將先來介紹有關以太坊中的交易手續費，而在後續 Etherum交易章節將會更詳細敘述交易相關內容。

## 交易手續費

以太坊的交易手續費包含兩個類型：轉帳以太幣的手續費與執行智能合約的手續費，手續費是以Gas來計算，Gas所花費的其實就是以太幣，但使用Gas來當作計算手續費的單位，Gas無法用來交易買賣，其計算方式為 :

```
交易手續費 Tx Fees = Gas Limit * Gas Price
```

其中Gas 計算包含兩個部分 : 分別為 Gas Limit 與 Gas Price

#### Gas Limit

相當於最多允許用多少單位個Gas來執行這個交易或是合約，發出交易時可以指定允許的Gas Limit，但如果低於該執行所需求的最低Gas Limit則會交易失敗，當合約的邏輯越多時，要執行它會需要耗費越多區塊鏈上的資源，所以自然會消耗較多單位個 Gas Limit，而Gas Limit 對於同一個Function來說是固定的，也就是不管是什麼時間來執行它，都會是固定單位的。

Limit字眼可以想像是，一個人最多想要花多少單位個Gas來執行這個合約Function，也就是容許的Gas數量上限。

每個區塊也有一個Gas Limit ，由挖礦的礦工所決定，正常情況下區塊內所有交易的Gas Limit總和不會超過區塊所設定的Gas Limit，代表著該區塊最多可以容納多少交易，如果要打包進區塊中的交易之Gas Limit超過區塊的Gas Limit則該筆交易無法被成功打包。

#### Gas Price

Gas Price為每單位Gas的價格，礦工們會依據此來決定納入交易的先後順序。

可以使用如下程式取得目前鏈上所設定的Gas Price

```
web3.eth.getGasPrice((err, result) => console.log(result))
```

> web3 將於後續章節說明。

可用以下網站查看目前以太坊上相關 Gas 資訊 :

> [https://ethgasstation.info/](https://ethgasstation.info/)

以太幣單位

```
kwei  (1000 Wei)
mwei  (1000 KWei)
gwei  (1000 mwei)
szabo (1000 gwei)
finney(1000 szabo)
ether (1000 finney)

所以一單位Ether = 1000000000000000000 Wei
```

> 後面講到web3 API後可用如下轉換
>
> ```
> web3.toWei(1); 
> web3.fromWei(1000000000000000000);
> ```

以太坊的挖礦算法名為：Ethash（Dagger-Hashimoto），會產生DAG（Dagger-part），是一個需求大量記憶體的演算法，可以用來抵抗特定專門挖礦的晶片。

有關Dag可參考如下文章：

> [https://github.com/ethereum/wiki/blob/master/Dagger-Hashimoto.md](https://github.com/ethereum/wiki/blob/master/Dagger-Hashimoto.md)
>
> [https://github.com/ethereum/wiki/wiki/Ethash-DAG](https://github.com/ethereum/wiki/wiki/Ethash-DAG)

## 改進未來Ethereum之Proposal

The Ethereum Improvement Proposal簡稱為EIP，為以太坊社群為了讓以太坊區塊鏈更完善而提出的改進項目。

[https://github.com/ethereum/EIPs](https://github.com/ethereum/EIPs)

## 有關以太坊 Implementations

以太坊有多個語言的實作

#### 1.Geth

Golang語言的實作

[https://github.com/ethereum/go-ethereum](https://github.com/ethereum/go-ethereum)

#### 2.Ethereumjs

Javascript語言的實作

[https://github.com/ethereumjs](https://github.com/ethereumjs)

#### 3.cpp-ethereum

C++語言的實作

[https://github.com/ethereum/cpp-ethereum](https://github.com/ethereum/cpp-ethereum)

#### 4.pyethereum

Python語言的實作

[https://github.com/ethereum/pyethereum](https://github.com/ethereum/pyethereum)

#### 5.parity

Rust語言的實作，主打輕量與快速

[https://github.com/paritytech/parity](https://github.com/paritytech/parity)

#### 6.ethereumj

Java語言的實作

[https://github.com/ethereum/ethereumj](https://github.com/ethereum/ethereumj)

#### 7.ethereum-ruby

Ruby語言的實作

[https://github.com/DigixGlobal/ethereum-ruby](https://github.com/DigixGlobal/ethereum-ruby)

#### 8.Nethereum

.NET語言的實作

[https://github.com/Nethereum/Nethereum](https://github.com/Nethereum/Nethereum)

#### 9.ethereum-haskell

haskell語言的實作

[https://github.com/bkirwi/ethereum-haskell](https://github.com/bkirwi/ethereum-haskell)

#### 10.ethereumex

Elixir語言的JSON-RPC client實作

[https://github.com/exthereum/ethereumex](https://github.com/exthereum/ethereumex)

## 區塊鏈狀態

可從此網站看到目前 Ethereum 區塊鏈狀態

[https://ethstats.net/](https://ethstats.net/)

![](/assets/012.png)

可以參考以下兩個 Github 專案自行架設上圖之網站，名為： [eth-netstats](https://github.com/cubedro/eth-netstats)

[https://github.com/cubedro/eth-net-intelligence-api](https://github.com/cubedro/eth-net-intelligence-api)

[https://github.com/cubedro/eth-netstats](https://github.com/cubedro/eth-netstats)

