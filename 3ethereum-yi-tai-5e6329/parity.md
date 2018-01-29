# Parity

[https://www.parity.io/](https://www.gitbook.com/book/easonwang01/e/edit#)

為一個輕量級的節點，可以快速進行節點的同步，並且在啟動後會自動開啟一個前端網頁，可以查看目前節點狀態。

安裝：[https://github.com/paritytech/parity/releases](https://github.com/paritytech/parity/releases)

文件：[https://paritytech.github.io/wiki/](https://paritytech.github.io/wiki/)

#### 路徑

安裝的可執行程式通常會再如下路徑

```
Windows: C:\Program Files\Ethcore\Parity
macOS:  /Applications/Parity\ Ethereum.app/Contents/MacOS:$PATH
Linux: /usr/bin/parity
```

> 將以上路徑加入環境變數即可直接在任何Terminal輸入Parity執行。

區塊鏈資料與Key通常會存在以下路徑：

```
Windows: %UserProfile%\AppData\Roaming\Parity
macOS: $HOME/Library/Application\ Support/io.parity.ethereum/
Linux: ~/.local/share/io.parity.ethereum/
```

#### 設定

[https://github.com/paritytech/parity/wiki/Configuring-Parity](https://github.com/paritytech/parity/wiki/Configuring-Parity)

Parity可以使用一個Toml檔案來進行相關設定，也可以使用此網站來線上產生設定檔[https://paritytech.github.io/parity-config-generator/。](https://paritytech.github.io/parity-config-generator/。)

預設會讀取以下路徑下的設定檔

```
Windows: %UserProfile%\AppData\Roaming\Parity\Ethereum\config.toml
macOS:  $HOME/Library/Application\ Support/io.parity.ethereum/config.toml
Linux: ~/.local/share/io.parity.ethereum/config.toml
```

也可以直接在啟動Parity時在CLI上加Flag：[https://github.com/paritytech/parity/wiki/Configuring-Parity\#cli-options](https://github.com/paritytech/parity/wiki/Configuring-Parity#cli-options)

#### 連線

如果我們有安裝Geth也可以使用Geth console連線到Parity

```
geth attach $HOME/Library/Application\ Support/io.parity.ethereum/jsonrpc.ipc
```

或是安裝Web3

```
npm install web3
```

接著使用Node.js的command line：

```
$ node
> Web3 = require("web3")
> web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
> web3.version

// 回傳相關資料
```



