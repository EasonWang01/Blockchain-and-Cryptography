# 2-3 Bitcoin 測試鏈(Testnet)
在區塊鏈有新功能的時候為了要先進行內部測試，所以會先將功能放到測試鏈上進行測試，但測試鏈的比特幣不具任何效力
https://en.bitcoin.it/wiki/Testnet

剛才我們所開啟的Bitcoin-QT錢包也可以與Testnet做連線

```
<...Path>/bitcoin-qt.exe -testnet -datadir="可指定要存放鏈資料的資料夾"
```
> Windows 在前面加上start
![](/assets/testnet-qt.png)

可以看到Bitcoin icon顯示為綠色

### 產生Testnet的地址

一樣在說明裡面開啟除錯視窗，然後輸入`getnewaddress`
![](/assets/testnet-address.png)
可看到產生`m`開頭的測試鏈地址

#### 線上產生

我們也可以用線上網站服務產生測試比特幣地址

https://www.bitaddress.org/bitaddress.org-v1.9-SHA1-a487b495d710d6f617d688e5f758e40c8b6c510e.html?testnet=true
> 記得後面要加上?testnet=true
![](/assets/testonlineaddress.png)
> 記得要按Generate New Address 產生新地址
並且記下address與Private key

(但還是建議讀者使用本地端產生的方式比較安全)


### 取得測試用的比特幣
