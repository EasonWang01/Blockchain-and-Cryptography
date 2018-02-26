# 閃電網路

## 概念

為了解決比特幣交易的高手續費與交易後確認時間過長的問題，決定把部分交易放在區塊鏈外執行，並於未來交易結算時才寫入區塊鏈中。

可參考此份論文：[https://lightning.network/lightning-network-paper.pdf。](https://lightning.network/lightning-network-paper.pdf。)

在閃電網路中，雙方要進行交易之前會預存一些資金到一個 multisignature 地址，之後雙方開啟一個支付通道（Channel）後，會維護一份共同的帳本，每次雙方有小額交易後都更新帳本，並且讓舊的帳本失效。

> 可以想像是一個小鎮上有一個大水塔，Ａ太太跟B太太都提了一些水回家，之後兩人放自己的一些水到兩人的共用水盆，之後需要水時，兩人可以不必到大水塔提水，先從共用水盆提水，然後紀錄哪位太太提了多少水即可。

其中有兩個名詞分別為 RSMC 與 HTLC。

RSMC（Recoverable Sequence Maturity Contract）定義了雙向小額支付通道的最基本工作方式。

```
1.小明與小黃兩人共同產生一個 2-of-2 multisig 地址，並且進行預存的動作。

2.小黃再產生一把私鑰2並且簽名，之後把簽名後的文件 Commitment Transactions 給小明，而小明也做同樣的動作，之後兩人都擁有對方所簽名的文件。

3.之後兩人的文件後續產生出的交易將會花費同一筆Output，所以只有其中一個人的可以廣播，這份文件廣播後只有對方可以先拿到餘額，
而經過1000個區塊確認（使用nSequence）後才會產生另一個交易，讓廣播的人可以拿到自己的餘額。

4.雙方有交易後，兩份文件的餘額會更動，而如果更動後有一方還想要廣播出舊的交易餘額的話會被懲罰 ( 在1000個區塊確認之前 ) ，懲罰的
方式是把違規方的所有的餘額交給另一方，其原理為：當有新的餘額時會將上份文件用來簽發的私鑰交給對方，而新的文件會用新的私鑰簽名，如果發現
小明關閉Channel時廣播出來的是舊的交易餘額文件，則因為小明在更新交易後也已經把舊的私鑰交給小黃了，所以小黃可以提出懲罰，用小明的舊的私鑰拿走
小明所有餘額。
```

HTLC（Hashed Timelock Contract）進一步利用nLockTime實現了有條件的資金支付，使多人之間的Channel支付變得可行。

```
假設一開始A太太想要付錢給C太太，但A太太只有跟B太太開了Channel，而B太太跟A和C都有開Channel可跟兩人溝通。


運作方式一開始C太太會產生一個私密訊息R，並用雜湊函式把R做Hash，然後把Hash值跟Hash function傳送給A太太。
之後使用 nTimelock 鎖定A太太的錢特定時間，在解鎖時刻到來之前，如果B太太能夠向A太太出示一個適當的R（稱為秘密），使得R的雜湊值等於事先約定的值H(R)，B太太就能獲得這鎖定的錢。但如果直到解鎖時刻過去B太太仍然未能提供一個正確的R，A太太被鎖定的錢將會歸還給A太太，所以B太太為了拿到錢也會開一個nTimelock 鎖定收據給C太太，讓C太太把R給他。

流程為：
C太太給Ａ太太，R被Hash過的值。
A太太開給B太太nTimelock的收據。
B太太再開給C太太nTimelock的收據。
C太太給出R，拿到B太太的錢。
B太太再把R給A拿到A太太的錢。
而B太太也可以從中獲得手續費。

（ 注意：如果A太太跟B太太之間的Channel過期時間比B太太跟C太太之間短，則C太太，太晚給出R，A太太在時間到後拿回錢，但這時C太太又把R給了B太太然後拿到錢，
但B太太就沒辦法再把R給A太太拿錢了，所以通常越接近 R 產生者的 channel 過期時間必須越短。）
```

官方影片：[https://youtu.be/8zVzw912wPo](https://youtu.be/8zVzw912wPo)

## 標準 \( specification \)

Lightning Network specification \( BOLTs \)， BOLT 全名為： Basic of Lightning Technologies。

有關閃電網路相關實作標準可參考：

[https://github.com/lightningnetwork/lightning-rfc](https://github.com/lightningnetwork/lightning-rfc)

![](/assets/ks9.png)

## 實作

我們將使用Lightning Network Daemon \( lnd \)，其已經實作相關比特幣閃電網路架構並且部署在testnet3上。

[https://github.com/lightningnetwork/lnd](https://github.com/lightningnetwork/lnd)

這裡我們會使用Docker來快速的啟動相關節點服務。

> Docker安裝可參考：[https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)

我們要先clone該專案，並且進入專案中的Docker資料夾。

> 為了避免未來版本更動造成程式無法執行，以下 Github 連結為筆者 Fork 原 lightningnetwork 專案之程式，保持讀者與本書具有相同專案版本。

```
git clone https://github.com/blockchain-tutorial-by-yicheng/lnd.git
cd lnd/docker
```

接下來我們會進行如下步驟

```
1. 啟動一個節點取名為 Alice

2. 啟動 btcd node 並且執行 private simnet，挖礦並將資金傳給Alice

3. 啟動一個節點取名為 Bob，並且與Alice連線

4. 開啟 Alice 與 Bob 的 Channel

5. Alice 使用 Channel 付款給 Bob

6. 關閉 Alice 與 Bob 間的Channel

7. 查看 Bob 的餘額
```

> btcd 是一個用Golang寫的Bitcoin full node
>
> [https://github.com/btcsuite/btcd](https://github.com/btcsuite/btcd)

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

![](/assets/螢幕快照 2018-01-21 下午5.25.32.png)之後在Alice或Bob的Terminal可以輸入以下，查看目前開啟的Channel

```
lncli listchannels
```

## 5.Alice使用Channel付款給Bob

先在Bob的Terminal輸入以下，產生相關付款單據。

```
lncli addinvoice --value=10000
```

![](/assets/螢幕快照 2018-01-21 下午5.32.00.png)

然後在Alice的Terminal輸入以下並填入剛才Bob產生出的`pay_req`，來付款給Bob

```
lncli sendpayment --pay_req=填入Bob產生的pay_req
```

![](/assets/螢幕快照 2018-01-21 下午5.34.14.png)

最後可以分別在兩人的Terminal輸入以下指令查看餘額：

```
lncli channelbalance
```

![](/assets/螢幕快照 2018-01-21 下午5.34.49.png)

## 6. 關閉 Alice 與 Bob 間的Channel

為了要關閉Channel，Alice需要兩個值，分別為 funding\_txid 以及 output\_index，我們可以先輸入以下指令：

```
lncli listchannels
```

之後可以看到在`channel_point`欄位，在分號前的是`funding_txid` 而分號後的是`output_index`

![](/assets/螢幕快照 2018-01-21 下午5.40.41.png)

所以我們在Alice的Terminal輸入以下指令來關閉Channel：

```
lncli closechannel --funding_txid=填入funding_txid --output_index=填入output_index
```

![](/assets/螢幕快照 2018-01-21 下午5.45.51.png)

## 7. 查看 Bob 的餘額

接著我們一樣產生三個新區塊來納入我們剛才關閉Channel的動作。

```
docker-compose run btcctl generate 3
```

然後分別查看Alice和Bob目前的餘額：

![](/assets/螢幕快照 2018-01-21 下午5.47.45.png)

---

Docker使用後可以以下指令移除相關Container

#### 停止所有container

```
docker stop $(docker ps -a -q)
```

#### 移除所有使用過的container名稱

在kill或stop container後要再把名稱移除才可再次重新使用

```
docker rm $(docker ps -q -f status=exited)
```



