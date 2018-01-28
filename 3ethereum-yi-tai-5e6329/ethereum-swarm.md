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
geth
```

然後啟動swarm

```
./swarm --bzzaccount 填入剛才帳號
```

![](/assets/螢幕快照 2018-01-28 下午11.32.06.png)

