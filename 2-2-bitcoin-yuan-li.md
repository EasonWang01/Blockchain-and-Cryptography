# 2-2 Bitcoin Core

在這一個章節，我們會開始實際使用比特幣相關的軟體

#### 安裝Bitcoin core

我們先到此比特幣官方下載網址[https://bitcoin.org/en/download](https://bitcoin.org/en/download)

1.選擇自己對應的平台安裝\(此安裝檔大小約為13.7MB\)  
![](/assets/bitcoin-core-download.png)

2.之後選擇一個擁有較大容量的硬碟安裝\(因為之後如果要成為Full Node 必須要足夠容量\)  
![](/assets/bitcoin-install01.png)

3.之後安裝好後執行，即可看到他開始在與其他節點同步區塊鏈資料  
![](/assets/bitcoin-qt01.png)

4.之後我們點選上方的`說明`然後點選`除錯視窗`  
![](/assets/bitcoin-qt02.png)

5.接著我們點選`除錯視窗`中的`主控台`  
\(這裡可以讓我們輸入一些指令\)  
![](/assets/bitcoin-qt03.png)

6.接著我們在`主控台`中輸入`help`可列出有哪些可用的指令

> 我們也可以在這個網站看到所有可用指令  
> [https://en.bitcoin.it/wiki/Original\_Bitcoin\_client/API\_calls\_list](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list)

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

#### 更改區塊鏈與錢包資料的存放資料夾

預設比特幣資料會存放在

Windows

```
C:\Documents and Settings\YourUserName\Application data\Bitcoin (XP)

C:\Users\YourUserName\Appdata\Roaming\Bitcoin (Vista and 7)
```

OSX

```
~/Library/Application\ Support/Bitcoin/
```

Linux

```
~/.bitcoin/
```

但我們也可以自己設定要存放的資料夾位置  
要這麼做的話，我們必須用命令列來啟動bitcoin程式，並且在指令上輸入我們要用的資料夾

步驟如下:

1.先用系統管理員打開命令列\(避免稍後打開資料夾權限不足\)  
2.之後輸入`cd` 後面加上我們剛才安裝的資料夾位置  
![](/assets/bitcoin-qt12.png)  
3.之後我們創建一個資料夾來存放我們要自己指定的區塊鏈資料存放位置\(這裡我們直接在這個資料夾下創建一個BitcoinData\)  
![](/assets/bitcoin-qt13.png)  
4.之後我們輸入如下指令\(記得確認路徑正確\)  
![](/assets/start-qt-cmd.png)  
之後他就會一樣開啟剛才的Bitcoin程式

> OSX與Linux不用在指令開頭加`start`

5.這時我們打開BitcoinData這個資料夾，即可看到裡面存放了錢包與區塊鏈的檔案

![](/assets/bitcoin-qt14.png)

其他資料夾位置資訊可參考以下

```
Windows
> C:\Users\YourUserName\Appdata\Roaming\Bitcoin

Linux
> ~/.bitcoin/

OSX
> ~/Library/Application\ Support/Bitcoin/

參考:
https://en.bitcoin.it/wiki/Data_directory
```

# 從 Terminal 或 Cmd 執行 bitcoin 指令

## Windows:

我們也可以從Terminal 或 Cmd 執行bitcoin 指令

1.一樣開啟terminal然後用進入到`daemon`路徑下  
![](/assets/bitcoin-qt15.png)

2.然後我們要先執行bitcoind\(其為Bitcoin server\)  
![](/assets/bitcoin-qt16.png)

3.之後可輸入

```
start ./bitcoin-cli getblockchaininfo
```

> Windows會在執行開一個新視窗顯示，但顯示完會隨即關閉視窗，所以如果要用此種方法建議寫一個batch file然後後面加上`pause`

## Linux, OSX

先到官網

[https://bitcoin.org/bin/](https://bitcoin.org/bin/)

選擇版本後，下載 [tar.gz](https://bitcoin.org/bin/bitcoin-core-0.15.1/bitcoin-0.15.1-osx64.tar.gz) 格式的安裝檔然後解壓縮 ，之後一樣 `cd`

到下載的資料夾目錄下的 /bin 資料夾

1.執行Bitcoin節點Server

```
./bitcoind
```

2.執行Command Line相關指令

```
./bitcoin-cli getblockchaininfo
```

3.停止server

```
./bitcoin-cli stop
```

4.啟動Server但不進行sync

```
./bitcoind -connect=0 -nolisten
```

> 啟動節點時也可輸入`./bitcoind -h` 查看相關可用標誌。
>
> 例如可以指定存放資料之資料夾：`./bitcoind -datadir="./chaindata"`
>
> 但記得使用Bitcoin-cli呼叫指令時，也要指定相同資料夾：`./bitcoin-cli -datadir="./chaindata" getblockchaininfo`

---

### 比特幣相關交易訊息與圖表

可以在以下網站看到目前的相關比特幣交易資訊

[https://blockchain.info/zh-cn/](https://blockchain.info/zh-cn/)

比特幣歷史訊息圖表

[https://blockchain.info/charts](https://blockchain.info/charts)

