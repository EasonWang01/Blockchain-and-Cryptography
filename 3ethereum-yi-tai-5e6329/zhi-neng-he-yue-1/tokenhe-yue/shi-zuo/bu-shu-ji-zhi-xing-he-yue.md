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

## 讓Ethereum wallet\(Mist\)讀取合約

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

## 授權轉帳

> 以下動作在Remix IDE與Mist均可執行。

接著要進入重頭戲，就是小明的爸爸授權給小明一張可以刷的副卡，額度為100

![](/assets/螢幕快照 2018-01-17 下午8.19.00.png)

授權了之後可以看到左側Allowance額度變為100，之後小明就可以隨意轉帳100 Token 給其他人，用的是爸爸的Token。

![](/assets/螢幕快照 2018-01-17 下午8.17.41.png)

## 購買Token

接著我們要讓帳戶4，傳送1單位的Ether給合約。

![](/assets/螢幕快照 2018-01-17 下午8.24.44.png)

之後查看帳戶4擁有的Token：

![](/assets/螢幕快照 2018-01-17 下午8.25.04.png)

> 因為Buy price為5，所以一單位Ether可以購買到五個token，後面的18位零為小數。

## 從合約提款

合約的部屬者可以使用withdraw function來進行從合約轉帳Ether到自己帳戶。

![](/assets/螢幕快照 2018-01-17 下午8.27.57.png)轉帳後可以看到合約的金額少了5 Ether

![](/assets/螢幕快照 2018-01-17 下午8.32.09.png)

> 有時Mist browser 交易成功執行後畫面沒有變，這時重新打開Mist即可。
>
> 如果相關執行條件都有符合但還是無法執行function可以把Mist重新開啟。



## 設定購買價格

使用setPrice Function設定每單位Ether可以換多少Token，之後可從Buy Price查看。![](/assets/螢幕快照 2018-01-17 下午8.49.48.png)

## 從合約提款

選擇Transfer並且輸入金額，會執行從合約部署者的帳號轉Token給該帳號。![](/assets/螢幕快照 2018-01-17 下午8.50.19.png)

## 移除合約





