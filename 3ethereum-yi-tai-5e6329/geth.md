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

https://github.com/ethereum/go-ethereum/wiki/Management-APIs\#list-of-management-apis

![](/assets/435345.png)

