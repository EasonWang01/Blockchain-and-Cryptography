# 閃電網路

## 概念

為了解決比特幣網路的高手續費以及交易確認時間過長的問題，決定把部分交易放在區塊鏈外執行，並於未來結算時才加入區塊鏈中。

可參考此份論文：[https://lightning.network/lightning-network-paper.pdf。](https://lightning.network/lightning-network-paper.pdf。)

雙方預存一些資金，並且在一開始會被放到一個multisignature "channel" 地址，雙方開啟一個支付通道後維護一份共同的帳本，每次雙方有小額交易後都更新帳本，並且舊的即失效，每次產生新的帳本餘額時都需要雙方的簽章驗證，

## 標準 \( specification \) 

Lightning Network specification \(BOLTs\)， BOLT 全名為： Basic of Lightning Technologies。

有關閃電網路相關實作標準可參考：

[https://github.com/lightningnetwork/lightning-rfc](https://github.com/lightningnetwork/lightning-rfc)

## 實作

我們將使用Lightning Network Daemon \( lnd \)，其已經實作相關比特幣閃電網路架構並且部署在testnet3上。

[https://github.com/lightningnetwork/lnd](https://github.com/lightningnetwork/lnd)

這裡我們會使用Docker來快速的啟動相關節點服務。

> Docker安裝可參考：https://docs.docker.com/engine/installation/

我們要先clone該專案，並且進入專案中的Docker資料夾。

```
git clone https://github.com/lightningnetwork/lnd.git
cd lnd/docker
```

接下來我們會進行如下步驟

```
啟動 btcd node 並且執行 private simnet.
啟動一個節點取名為 Alice
啟動一個節點取名為 Bob
挖礦並將資金傳給Alice
開啟 Alice 與 Bob 的 Channel
Alice 付款給 Bob.
關閉 Alice 與 Bob 間的Channel

查看 Bob 的餘額
```



