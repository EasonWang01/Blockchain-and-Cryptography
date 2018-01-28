# Ethereum Swarm

[http://swarm-guide.readthedocs.io/en/latest/introduction.html](http://swarm-guide.readthedocs.io/en/latest/introduction.html)

```
git clone https://github.com/ethereum/go-ethereum
cd go-ethereum
make all
```

> 使用`make all`編譯之前需要先安裝好Golang環境：[https://golang.org/doc/install](https://golang.org/doc/install)

之後進入到`/build/bin`資料夾可以看到如下

![](/assets/螢幕快照 2018-01-28 下午11.21.32.png)

之後我們新增一個帳號

```
./geth account new
```

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

接著我們試著存入一筆文字，開啟另一個Terminal然後輸入以下

```
curl -H "Content-Type: text/plain" --data-binary "every 60 seconds a minute passes" http://localhost:8500/bzz:/
```

其會產生一串Hash

我們一樣使用`curl`試著去讀取他，即可看到剛才的字串

> 路徑為`http://localhost:8500/bzz:/剛才的Hash`

![](/assets/螢幕快照 2018-01-28 下午11.45.46.png)

> 如果Hash最後面出現如上圖的`%` 則把它替換為`/`加在路徑最後即可



