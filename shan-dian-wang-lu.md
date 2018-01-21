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

> Docker安裝可參考：[https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)

我們要先clone該專案，並且進入專案中的Docker資料夾。

```
git clone https://github.com/lightningnetwork/lnd.git
cd lnd/docker
```

接下來我們會進行如下步驟

```
1. 啟動一個節點取名為 Alice

2. 啟動 btcd node 並且執行 private simnet，挖礦並將資金傳給Alice

3. 啟動一個節點取名為 Bob，並且與Alice連線

4. 開啟 Alice 與 Bob 的 Channel

5. Alice 使用Channel付款給 Bob

6. 關閉 Alice 與 Bob 間的Channel

7. 查看 Bob 的餘額
```

> btcd 是一個用Golang寫的Bitcoin full node
>
> https://github.com/btcsuite/btcd

## 1.設定環境變數並且啟動alice節點

> 記得要先啟動安裝好的Docker。

```
export NETWORK="simnet"
docker-compose run -d --name alice lnd_btc
```

進入alice的Terminal，並且產生一個P2SH地址。

```
docker exec -i -t alice bash
lncli newaddress np2wkh
```

![](/assets/螢幕快照 2018-01-21 下午4.50.11.png)

## 2.啟動一個btcd節點，並將Alice地址設為挖礦獎勵地址。

開啟另外一個Terminal然後一樣進入到相同目錄`cd lnd/docker` 輸入以下：

```
MINING_ADDRESS=填入剛才產生的地址 docker-compose up -d btcd
```

接著新增400個區塊

```
docker-compose run btcctl generate 400
```

![](/assets/螢幕快照 2018-01-21 下午4.57.36.png)確認segwit為啟動狀態。

```
docker-compose run btcctl getblockchaininfo | grep -A 1 segwit
```

![](/assets/螢幕快照 2018-01-21 下午5.00.10.png)最後回到alice的Terminal查看她的餘額：

```
lncli walletbalance --witness_only=true
```

## ![](/assets/螢幕快照 2018-01-21 下午5.00.59.png)

## 3.啟動Bob節點

```
docker-compose run -d --name bob lnd_btc
docker exec -i -t bob bash
```

之後為了和Alice連線我們要取得Bob的idenity pubkey

```
lncli getinfo
```

![](/assets/螢幕快照 2018-01-21 下午5.04.34.png)然後開一個新的Terminal查看Bob的Docker節點IP

```
docker inspect bob | grep IPAddress
```

![](/assets/螢幕快照 2018-01-21 下午5.19.38.png)

最後在Alice 的 terminal 輸入如下即可連線。

```
lncli connect <bob_pubkey>@<bob_host>
```

![](/assets/螢幕快照 2018-01-21 下午5.08.23.png)並且可輸入以下指令檢查已連線節點：

```
lncli listpeers
```

## 4.開啟 Alice 與 Bob 的 Channel

在Alice的Terminal輸入以下：

```
lncli openchannel --node_key=填入Bob的identity_pubkey --local_amt=1000000
```

> --local\_amt=1000000 代表alice預先存入該Channel金額1000000 satoshis

之後創建三個區塊，來納入剛才的funding transaction

```
docker-compose run btcctl generate 3
```

![](/assets/螢幕快照 2018-01-21 下午5.25.32.png)之後再Alice或Bob的Terminal可以輸入以下，查看目前開啟的Channel

```
lncli listchannels
```

## 5.Alice使用Channel付款給Bob



