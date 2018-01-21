# 閃電網路

## 概念

為了解決比特幣網路的高手續費以及交易確認時間過長的問題，決定把部分交易放在區塊鏈外執行，並於未來結算時才加入區塊鏈中。

可參考此份論文：[https://lightning.network/lightning-network-paper.pdf。](https://lightning.network/lightning-network-paper.pdf。)

雙方預存一些資金，並且在一開始會被放到一個multisignature "channel" 地址，雙方開啟一個支付通道後維護一份共同的帳本，每次雙方有小額交易後都更新帳本，並且舊的即失效，每次產生新的帳本餘額時都需要雙方的簽章驗證，

## 標準

有關閃電網路相關實作標準可參考：

https://github.com/lightningnetwork/lightning-rfc

## 實作

我們將使用Lightning Network Daemon \( lnd \)，其已經實作相關比特幣閃電網路架構並且部署在testnet3上。

[https://github.com/lightningnetwork/lnd](https://github.com/lightningnetwork/lnd)

