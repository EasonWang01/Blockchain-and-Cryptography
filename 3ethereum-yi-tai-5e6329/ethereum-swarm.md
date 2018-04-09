# Ethereum Swarm

[http://swarm-guide.readthedocs.io/en/latest/introduction.html](http://swarm-guide.readthedocs.io/en/latest/introduction.html)

[https://github.com/ethersphere/swarm-guide/blob/master/contents/usage.rst](https://github.com/ethersphere/swarm-guide/blob/master/contents/usage.rst)

類似於IPFS （ 註一 ） ，可以把檔案上傳到各地的節點，目標為分散式的檔案存儲，沒有中心Server。

Web3的bzz模組有其相關API可使用。

[https://web3js.readthedocs.io/en/1.0/web3-bzz.html](https://web3js.readthedocs.io/en/1.0/web3-bzz.html)

#### 安裝

```
git clone https://github.com/ethereum/go-ethereum
cd go-ethereum
make all
```

> 使用`make all`編譯之前需要先安裝好Golang環境：[https://golang.org/doc/install](https://golang.org/doc/install)

之後進入到`/build/bin`資料夾可以看到如下

![](/assets/螢幕快照 2018-01-28 下午11.21.32.png)

#### 使用

我們先新增一個帳號

```
./geth account new --datadir="./swarm_test" 
```

> 如果已經有帳號，可以輸入 `./geth account list --datadir="./swarm_test"`查看帳號

![](/assets/螢幕快照 2018-01-28 下午11.28.57.png)

接著開啟另一個Terminal啟動geth

```
geth --datadir="./swarm_test" --ipcpath="~/Library/Ethereum/geth.ipc"
```

> 也可以不指定datadir與ipcpath

然後啟動swarm

```
./swarm --bzzaccount 填入剛才帳號
```

![](/assets/螢幕快照 2018-01-28 下午11.32.06.png)

接著我們來試著存入一筆文字，開啟另一個Terminal然後輸入以下

```
curl -H "Content-Type: text/plain" --data-binary "every 60 seconds a minute passes" http://localhost:8500/bzz:/
```

執行後會產生一串Hash

我們一樣使用`curl`試著去讀取他，即可看到剛才的字串

> 路徑為`http://localhost:8500/bzz:/剛才的Hash`

![](/assets/螢幕快照 2018-01-28 下午11.45.46.png)

> 如果Hash最後面出現如上圖的`%` 則把它替換為`/`加在路徑最後即可

也可用網頁讀取：

![](/assets/螢幕快照 2018-01-28 下午11.46.15.png)

#### 上傳檔案

可以輸入以下指令上傳檔案

```
./swarm up 檔案路徑
```

#### 上傳目錄

> 先如下創建資料夾及檔案
>
> ```
> mkdir testdir
> echo "test" >> ./testdir/test.txt
> ```

```
./swarm --recursive up ./testdir
```

之後到以下連結即可看檔案

```
http://localhost:8500/bzz:/回傳之Hash/test.txt
```

> 如果沒有加入檔案名稱在Hash後則會顯示找不到檔案

---

註一：

IPFS為一個可以將檔案上傳至分散式節點的專案，上傳後會回傳Hash，之後可以使用此Hash再次下載資料，不需要自己建立資料庫儲存檔案。

官方網站：[https://ipfs.io/](https://ipfs.io/)

官方Github : [https://github.com/ipfs/ipfs](https://github.com/ipfs/ipfs)

