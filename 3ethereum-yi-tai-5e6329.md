# 以太坊（Ethereum）簡介

以太坊是一個開源的區塊鏈平台，和比特幣一樣具有挖礦機制，但其更具特色的是具有智能合約系統，開發者可以撰寫智能合約然後給以太坊的虛擬機\(EVM\)進行編譯，然後部署到區塊鏈上，部署後的合約可以被其他人執行，並執行合約上對應的功能。

每個被部署到以太坊區塊鏈的智能合約都具有一個地址。

以太坊的代幣稱為以太幣\(ETH\)，可用來交易與支付執行智能合約的手續費，但手續費通常是以Gas來計算，Gas類似於交易的手續費，其計算方式為

```
交易手續費 Tx Fees = Gas Limit * Gas Price
```

其中

```
Gas Price: 每單位Gas多少錢( 會變動 )
Gas Limit: 多少單位個Gas( 通常不會變動 )
```

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

## 改進未來Ethereum之Proposal

The Ethereum Improvement Proposal簡稱為EIP，為以太坊社群為了讓以太坊區塊鏈更完善而提出的改進項目。

[https://github.com/ethereum/EIPs](https://github.com/ethereum/EIPs)

## 有關以太坊Implementations

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

[https://github.com/ethereum/pyethereum](https://github.com/ethereum/pyethereum)

python語言的實作

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

可從此網站看到目前區塊鏈狀態

[https://ethstats.net/](https://ethstats.net/)

![](/assets/012.png)

並且可以自行架設

[https://github.com/cubedro/eth-net-intelligence-api](https://github.com/cubedro/eth-net-intelligence-api)

[https://github.com/cubedro/eth-netstats](https://github.com/cubedro/eth-netstats)

