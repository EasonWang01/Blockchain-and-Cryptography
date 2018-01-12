# Geth

為Golang寫成的Ethereum客戶端指令介面。

# 安裝

[https://geth.ethereum.org/downloads/](https://geth.ethereum.org/downloads/)

![](/assets/432.png)或是可參考其他安裝方法，包含使用Docker執行Geth

[https://geth.ethereum.org/install/!\[\]\(/assets/324.png](https://geth.ethereum.org/install/![]%28/assets/324.png)\)

# 使用

Geth提供的API endpoint包含RPC HTTP endpoint、Websocket endpoint、unix socket \(Unix\) 或 named pipe \(Windows\)之IPC endpoint可使用。

輸入

```
geth console
```

即可進入 geth 的指令介面

> 也可以先在A terminal輸入geth，然後開啟另一個terminal輸入geth attach，開啟console

![](/assets/234324234.png)

之後再這邊可以輸入指令，指令列表可參考

[https://github.com/ethereum/go-ethereum/wiki/Management-APIs\#list-of-management-apis](https://github.com/ethereum/go-ethereum/wiki/Management-APIs#list-of-management-apis)

![](/assets/435345.png)

# Geth啟動參數

Geth再啟動時可以在指令上加上flag參數，讓Geth使用不同的狀態執行。

e.g.

```
geth.exe --fast --cache 1024

// --fast意思為只下載區塊鏈上的狀態而不下載所有區塊鏈上所有的資料，可以快速地進行同步，但只限於第一次同步時輸入
// --cache=1024 意思為設定電腦上的RAM保存區塊鏈上的資料最大快取為1024MB
```

# 將metamask的帳號import到Geth

先到metamask將private key取出

![](/assets/螢幕快照 2018-01-12 上午9.17.50.png)

然後進入到Geth指令介面後輸入如下：

```
personal.importRawKey("<Private Key>","<New Password>")
```



