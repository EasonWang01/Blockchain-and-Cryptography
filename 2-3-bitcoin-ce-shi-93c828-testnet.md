# 2-3 Bitcoin 測試鏈\(Testnet\)

在區塊鏈有新功能的時候為了要先進行內部測試，所以會先將功能放到測試鏈上進行測試，但測試鏈的比特幣不具任何實際價值。  
[https://en.bitcoin.it/wiki/Testnet](https://en.bitcoin.it/wiki/Testnet)

剛才我們所開啟的Bitcoin-QT錢包也可以與Testnet做連線

```
<...Path>/bitcoin-qt.exe -testnet -datadir="可指定要存放鏈資料的資料夾"
```

> Windows 在前面加上start  
> ![](/assets/testnet-qt.png)

可以看到Bitcoin icon顯示為綠色

### 產生Testnet的地址

一樣在說明裡面開啟除錯視窗，然後輸入`getnewaddress`  
![](/assets/testnet-address.png)  
可看到產生`m`開頭的測試鏈地址

#### 線上產生

我們也可以用線上網站服務產生測試比特幣地址

[https://www.bitaddress.org/bitaddress.org-v1.9-SHA1-a487b495d710d6f617d688e5f758e40c8b6c510e.html?testnet=true](https://www.bitaddress.org/bitaddress.org-v1.9-SHA1-a487b495d710d6f617d688e5f758e40c8b6c510e.html?testnet=true)

> 記得後面要加上?testnet=true  
> ![](/assets/testonlineaddress.png)  
> 記得要按Generate New Address 產生新地址  
> 並且記下address與Private key

\(但還是建議讀者使用本地端產生的方式比較安全\)

### 取得測試用的比特幣

我們可以到[https://testnet.manu.backend.hamburg/faucet](https://testnet.manu.backend.hamburg/faucet)

這個網站取得免費測試用的比特幣

![](/assets/getfreecoin.png)

成功後會出現交易的TXid

![](/assets/getfreecoin1.png)

之後我們可以點擊下去確認

![](/assets/getfreecoin2.png)

然後我們到blockexplorer這個網站確認我們地址的餘額

> 在本地端的節點必須要完整同步後才可以得到正確的區塊鏈資訊，所以建議讀者可使用線上的網站服務，他會去存取他們已經完整同步過的Full Node，然後回傳資訊到網站上供瀏覽

![](/assets/getfreecoin3.png)

