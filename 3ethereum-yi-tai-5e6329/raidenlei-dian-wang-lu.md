# Raiden雷電網路

我們使用Parity進行Ropsten測試網路的sync。

同步後的畫面如下

![](/assets/螢幕快照 2018-01-29 上午12.15.43.png)

其區塊鏈資料存放在如下位置

```
MAC  $HOME/Library/Application\ Support/io.parity.ethereum/
Windows %UserProfile%\AppData\Roaming\Parity
Linux ~/.local/share/io.parity.ethereum/
```

如果我們有安裝Geth也可以使用Geth console連線到Parity

```
geth attach $HOME/Library/Application\ Support/io.parity.ethereum/jsonrpc.ipc
```



