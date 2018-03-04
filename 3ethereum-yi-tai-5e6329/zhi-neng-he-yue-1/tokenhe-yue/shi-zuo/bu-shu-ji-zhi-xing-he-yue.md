# 部署及執行合約

這邊我們會使用兩個工具

```
Remix IDE  (http://remix.ethereum.org)
Mist       (https://github.com/ethereum/mist)
```

Remix IDE先前已經介紹過，這邊我們要來下載Mist，Mist是一個可以部署與執行智能合約的以太坊錢包。

> Mist下載頁面：[https://github.com/ethereum/mist/releases](https://github.com/ethereum/mist/releases)

我們剛才已經啟動一個私有鏈節點，這時我們可以直接開啟Mist，會自動連線到該私有鏈節點，如果沒有連線到私有鏈的話可以用如下方式啟動，幫他指定要連線的RPC server位置。

macOS :

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

> 在Remix IDE右側畫面，如果是constant, view 或是 public的變數均可直接點選，查看數值。

#### 

## 讓Ethereum wallet \(Mist\) 讀取合約

我們在Remix IDE點選compile然後選擇ERC20\_token \( 剛才合約檔案名稱 \)，最後按下Details按鈕。

#### ![](/assets/螢幕快照 2018-01-16 下午10.29.47.png)

然後拉到下方，點擊ABI中的複製按鈕。

![](/assets/螢幕快照 2018-01-16 下午10.32.09.png)

再來我們開啟剛才執行的Ethereum wallet \( Mist \)，並且選擇上方Contract Tab之後點擊Watch Contract按鈕。

![](/assets/螢幕快照 2018-01-16 下午10.35.31.png)

接著分別填上：合約地址、合約名稱、合約ABI

![](/assets/螢幕快照 2018-01-16 下午10.38.28.png)

新增後即可點擊，進入我們新增的合約

![](/assets/螢幕快照 2018-01-16 下午10.44.47.png)

進入後拉到下方，在`Balance of` 欄位填入剛才用來部署合約的地址，即可看到目前其擁有的token數量。![](/assets/螢幕快照 2018-01-16 下午10.43.55.png)

> 我們剛才輸入的 Token 總供給量為 2000 個，但因為小數點為18，所以會顯示2000 \* \(10 \*\* 18\)的數字。

> 2000後面的0均代表小數點。

## 授權轉帳

> 以下動作在Remix IDE與Mist均可執行。

接著要進入重頭戲，假設小明的爸爸授權給小明一張可以刷的副卡，額度為100，我們使用合約中的Allowance Function

![](/assets/螢幕快照 2018-01-17 下午8.19.00.png)

授權了之後可以看到左側Allowance額度變為100，之後小明就可以隨意轉帳100 Token 給其他人，用的是爸爸的Token。

![](/assets/螢幕快照 2018-01-17 下午8.17.41.png)

## 購買Token

接著我們要讓帳戶4，傳送1單位的Ether給合約，使用合約中的Buy  Function。

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

選擇Transfer並且輸入帳號與金額，會從合約部署者的帳號轉Token給指定帳號。![](/assets/螢幕快照 2018-01-17 下午8.50.19.png)

## 移除合約

使用Delete Contract Function可以從區塊鏈永久移除此合約，並且將合約上的Ether全額轉帳到部署合約的帳號。![](/assets/螢幕快照 2018-01-17 下午9.09.21.png)

之後合約點擊進去變成空的，及代表此合約已經成功從區塊鏈上移除。

![](/assets/螢幕快照 2018-01-17 下午9.09.45.png)

