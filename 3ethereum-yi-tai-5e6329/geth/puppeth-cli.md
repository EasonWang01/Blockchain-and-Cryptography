# Proof of Authority 與 Puppeth CLI

Geth 在 1.6 版本後新增了一個可以用互動式指令創建區塊鏈的工具，名為：Puppeth CLI。

以下我們會用Puppeth CLI建立一個PoA版本的私有鏈。

PoA \(Proof of Authority\)在Parity的客戶端軟體也有實作，但和 go-ethereum兩者實作的方式稍微不同，有關 go-ethereum 版本的實作可參考：[https://github.com/ethereum/EIPs/issues/225](https://github.com/ethereum/EIPs/issues/225)

> go-ethereum 版本的 PoA 為 Clique proof-of-authority consensus protocol
>
> 原始碼：[https://github.com/ethereum/go-ethereum/blob/master/consensus/clique/clique.go](https://github.com/ethereum/go-ethereum/blob/master/consensus/clique/clique.go)

開始前必須先擁有一個以太坊account，如果沒有可以如下新增

```
geth --datadir ./eth_clique_test account new
```

1.

進入後先輸入一個network 名稱 ![](/assets/螢幕快照 2018-01-13 上午8.49.18.png)2.

然後選擇要進行的動作

![](/assets/螢幕快照 2018-01-13 上午9.12.31 1.png)

分別為：

```
1. 顯示區塊鏈網路狀態
2. 設定創世區塊
3. 新增遠端節點
4. 部署相關服務元件
```

3.

接著選擇共識系統，我們選擇第二個選項 \( Clique \)

![](/assets/螢幕快照 2018-01-13 上午9.17.16.png)

> 預設即為Clique ，上面第一個選項為工作量證明的挖礦共識系統

4.

接著會詢問每個Block產生要多少秒，這裡我們保持預設，直接按下Enter

![](/assets/螢幕快照 2018-01-13 上午9.21.41.png)

5.

接著會詢問哪個帳號可以被授權打包區塊，我們輸入剛才創建的帳號

![](/assets/螢幕快照 2018-01-13 上午9.22.47.png)

> 輸入完後會跳下一行，然後顯示0x，可以繼續輸入其他帳號，如果不輸入就直接點選Enter

6.

再來會是詢問哪些帳號要先分配Ether這邊一樣輸入剛才新增的帳號

![](/assets/螢幕快照 2018-01-13 上午9.25.35 1.png)

7.

最後兩個問題分別是要給 chain/network 一個ID，以及要納入創世區塊的訊息，這邊我們兩個問題都直接按Enter

![](/assets/螢幕快照 2018-01-13 上午9.27.29.png)

8.

再來點選2，即可儲存剛才設定的創世區塊，並且輸入我們要將它存放的檔案路徑即可。

![](/assets/螢幕快照 2018-01-13 上午9.30.22.png)

9.

我們進到剛才存放genesis.json創世區塊的資料夾，輸入：

```
geth  --datadir="./" init genesis.json

之後開啟另一個Terminal輸入

geth  --datadir="./node1" init genesis.json
```

> 上面用途為使用創世區塊初始化兩個節點

10.

接著啟動節點

```
geth --datadir="./" console

另一個Terminal輸入

geth --datadir="./node1" --port 30304 console
```

11.

接著使用`admin.nodeInfo`與`admin.addPeer`讓兩節點連線

![](/assets/螢幕快照 2018-01-13 上午10.39.07.png)12.

在第二個節點 \( node1 \) 新增一個帳號

```
personal.newAccount()
```

13.

之後在第一個節點輸入以下指令，進行解鎖帳號以及轉帳動作

解鎖：

```
personal.unlockAccount(eth.accounts[0])
```

轉帳：

```
eth.sendTransaction({from: eth.accounts[0], to: "0xdf6d9ad96b630e06325ceb9f7e23a86695997421", value: web3.toWei(0.05777, "ether")})
```

> 因為我們在創世區塊有預先分配 Ether 給第一個節點的帳號，所以有餘額可以進行轉帳。

14.

接著分別於兩節點輸入挖礦指令，比較結果。

```
miner.start(1)
```

![](/assets/123.png)

> 可以看到左側之節點1的Etherbase account，在一開始創世區塊已經設定為PoA授權帳號，所以可以成功打包區塊，而右側節點帳號不是認證的，所以打包失敗。

如果節點1把Etherbase更換為一個不是PoA授權帳號，則也一樣不可以打包區塊，因為授權是判斷帳號不是判斷節點。

例如在節點1輸入如下：

```
personal.newAccount();  // "0x2384743aed7eba7a6d467eaa2d10fbedcf25346a"

miner.setEtherbase("0x2384743aed7eba7a6d467eaa2d10fbedcf25346a");

miner.start(1);
```

會產生

```
WARN [01-13|10:51:37] Block sealing failed  err=unauthorized
```

> 如果是認證的授權帳號，但是節點沒有跟任何其他節點連線，則miner.start\(1\)一樣不會有反應。

15.

我們可以把其他帳號一起加入PoA認證列表

```
clique.propose("0xdf6d9ad96b630e06325ceb9f7e23a86695997421", true)
```

> 必須要在Etherbase本身是認證帳號的情況下才可以使用此指令，授權其他帳號加入認證帳號。

授權了以後，其他節點也可以正常打包區塊了。

![](/assets/螢幕快照 2018-01-13 上午11.20.31.png)

> 如果輸入miner.start\(1\)後只產生null訊息，然後什麼事也沒發生，這時先輸入miner.stop\(\)，之後再試一次看看。

## Clique相關指令

![](/assets/螢幕快照 2018-01-13 上午11.22.13.png)

1.clique.getSigners\(\)

可以回傳目前被授權之帳號![](/assets/螢幕快照 2018-01-13 上午11.31.10.png)

2.clique.getSnapshot\(\)

可以回傳目前clique的狀態![](/assets/螢幕快照 2018-01-13 上午11.31.50.png)

3.clique.propose\("&lt;帳號&gt;", true\)

對一個帳號提出提案，提案成功後會將該帳號加入授權列表。

提案中的帳號會列在如下：

![](/assets/螢幕快照 2018-01-13 上午11.43.19.png)

4.clique.discard\("&lt;帳號&gt;"\)

取消對某一個帳號的提案

```
範例：
clique.discard("0xdf6d9ad96b630e06325ceb9f7e23a86695997422")
```

之後他會從proposals列表移除。

可以參考：

[https://github.com/ethereum/go-ethereum/blob/master/consensus/clique/api.go](https://github.com/ethereum/go-ethereum/blob/master/consensus/clique/api.go)

