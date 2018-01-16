# 部署及執行合約

這邊我們會使用兩個工具

```
Remix IDE(http://remix.ethereum.org)
Mist     (https://github.com/ethereum/mist)
```

Remix IDE先前已經介紹過，這邊我們要來下載Mist，Mist是一個可以部署與執行智能合約的以太坊錢包。

Mist下載頁面：[https://github.com/ethereum/mist/releases](https://github.com/ethereum/mist/releases)

我們剛才已經啟動一個私有鏈節點，這時我們可以直接開啟Mist，會自動連線到該節點，如果沒有的話可以用如下方式啟動，幫他指定要連線的RPC server位置。

OSX :

```
/Applications/Ethereum\ Wallet.app/Contents/MacOS/Ethereum\ Wallet --rpc http://localhost:8545
```

之後會要求確認，點擊『 好 』。![](/assets/螢幕快照 2018-01-16 下午10.01.51.png)

點選確認之後，看到右上有『 PRIVATE-NET 』字樣即為成功連線到本地節點。

![](/assets/螢幕快照 2018-01-16 下午10.03.02.png)

進入畫面後即可看到我們的節點帳號資料![](/assets/螢幕快照 2018-01-16 下午10.05.07.png)

再來回到Remix IDE，把剛才的合約部署。

![](/assets/螢幕快照 2018-01-16 下午10.14.49.png)

部署後即可看到如下畫面：![](/assets/螢幕快照 2018-01-16 下午10.21.28.png)

> 在Remix IDE右側畫面，如果是constant或是public的常數均可直接點選，查看數值。

#### 

#### 讓Ethereum wallet\(Mist\)讀取合約

我們在Remix IDE點選compiler然後選擇ERC20\_token，最後按下Details按鈕。

#### ![](/assets/螢幕快照 2018-01-16 下午10.29.47.png)

然後拉到下方，點擊ABI中的複製按鈕。

![](/assets/螢幕快照 2018-01-16 下午10.32.09.png)

再來我們開啟剛才執行的Ethereum wallet\(Mist\)

![](/assets/螢幕快照 2018-01-16 下午10.35.31.png)

接著分別填上：合約地址、合約名稱、合約ABI

![](/assets/螢幕快照 2018-01-16 下午10.38.28.png)

之後點擊進入我們新增的合約

![](/assets/螢幕快照 2018-01-16 下午10.44.47.png)

進入後拉到下方，在Balance of 欄位填入剛才用來部署合約的地址，即可看到目前其擁有的token數量。![](/assets/螢幕快照 2018-01-16 下午10.43.55.png)

> 我們剛才輸入2000個Token總供給量，但因為小數點為18，所以會顯示2000 \* \(10 \*\* 18\)的數字。
>
> 2000後面的0均代表小數點。



