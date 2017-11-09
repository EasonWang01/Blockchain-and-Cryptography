# 2-2 Bitcoin 初探
在這一個章節，我們會開始實際使用比特幣相關的軟體

#### 安裝Bitcoin core
我們先到此比特幣官方下載網址https://bitcoin.org/en/download

1.選擇自己對應的平台安裝(此安裝檔大小約為13.7MB)
![](/assets/bitcoin-core-download.png)

2.之後選擇一個擁有較大容量的硬碟安裝(因為之後如果要成為Full Node 必須要足夠容量)
![](/assets/bitcoin-install01.png)

3.之後安裝好後執行，即可看到他開始在與其他節點同步區塊鏈資料
![](/assets/bitcoin-qt01.png)

4.之後我們點選上方的`說明`然後點選`除錯視窗`
![](/assets/bitcoin-qt02.png)

5.接著我們點選`除錯視窗`中的`主控台` 
(這裡可以讓我們輸入一些指令)
![](/assets/bitcoin-qt03.png)

6.接著我們在`主控台`中輸入`help`可列出有哪些可用的指令
> 我們也可以在這個網站看到所有可用指令
https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list

![](/assets/bitcoin-qt06.png)
7.我們可輸入`getnewaddress`來在錢包中產生一個比特幣地址
![](/assets/bitcoin-qt07.png)

8.接著可點擊上方的`檔案`然後點選`收款位置`查看剛產生出的地址
![](/assets/bitcoin-qt08.png)

並且可以幫他加上標記，方便我們辨識這個地址
![](/assets/bitcoin-qt09.png)

#### 備份錢包

點選`檔案`內的`備份錢包`
![](/assets/bitcoin-qt10.png)
然後他會產生一個`.dat`檔案
![](/assets/bitcoin-qt11.png)

