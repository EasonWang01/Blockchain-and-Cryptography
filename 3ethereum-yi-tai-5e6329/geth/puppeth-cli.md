# Puppeth CLI

Geth 在 1.6版本後新增了一個可以用互動式指令創建區塊鏈的工具，名為：Puppeth CLI。

以下我們會用Puppeth CLI建立一個POA版本的私有鏈。

POA \(Proof of Authority\)在Parity的客戶端軟體也有實作，但和 go-ethereum兩者實作的方式稍微不同，有關go-ethereum版本的可參考：[https://github.com/ethereum/EIPs/issues/225](https://github.com/ethereum/EIPs/issues/225)

> go-ethereum版本其為Clique proof-of-authority consensus protocol

開始前必須先擁有一個以太坊account，如果沒有可以如下新增

```
geth --datadir ./eth_clique_test account new
```

1.

進入後先輸入一個network 名稱 \(隨意輸入\)![](/assets/螢幕快照 2018-01-13 上午8.49.18.png)2.

然後選擇你要做的事

![](/assets/螢幕快照 2018-01-13 上午9.12.31 1.png)

分別為：

```
1. 顯示區塊鏈網路狀態
2. 設定創世區塊
3. 新增遠端節點
4. 部署相關服務元件
```

1. 我們選擇第二個選項

![](/assets/螢幕快照 2018-01-13 上午9.17.16.png)

> 預設即為clique ，上面第一個選項為挖礦共識系統

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

再來的最後兩個問題分別是要給chain/network 一個ID，以及要納入創世區塊的訊息，這邊我們兩個問題都直接按Enter

![](/assets/螢幕快照 2018-01-13 上午9.27.29.png)

8.

最後點選2，即可儲存剛才設定的創世區塊，並且輸入我們要將它存放的檔案路徑即可。

![](/assets/螢幕快照 2018-01-13 上午9.30.22.png)

9.

再來我們進到剛才存放genesis.json創世區塊的資料夾，輸入：

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

接著使用admin.nodeInfo與admin.addPeer讓兩節點連線

![](/assets/螢幕快照 2018-01-13 上午10.39.07.png)12.

在第二個節點\(node1\)新增一個帳號

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

> 因為我們在創世區塊有預先分配Ether給第一個節點的帳號

14.

接著方別再兩節點輸入挖礦指令，比較結果

```
miner.start(1)
```

![](/assets/123.png)

> 可以看到左側之節點1的Etherbase account，在一開始創世區塊已經設定為POA授權帳號，所以可以成功打包區塊，而右側節點帳號不是認證的，所以打包失敗。

如果節點1把Etherbase更換為一個不是POA授權帳號，則也一樣不可以打包區塊，因為授權是判斷帳號不是判斷節點。

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



