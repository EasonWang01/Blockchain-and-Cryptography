# Geth （go-ethereum）

Geth 為 Golang 寫成的 Ethereum 用戶端指令介面 \( go-ethereum \)，為目前許多人所使用的節點程式。

# 安裝

以下提供三種安裝方法。

[https://geth.ethereum.org/downloads/](https://geth.ethereum.org/downloads/)

#### 下載安裝包

#### ![](/assets/432.png)

#### 使用Docker執行Geth

#### ![](/assets/2.png)

#### 從原始碼編譯

```
git clone https://github.com/ethereum/go-ethereum
cd go-ethereum
make geth
```

> 需要先安裝好Golang相關環境才可編譯：[https://golang.org/doc/install](https://golang.org/doc/install)
>
> 如果是自己從原始碼編譯，記得要將編譯好後產生的 `bin` 資料夾加入電腦的環境變數，才能從Terminal之直接執行Geth

# 使用

Geth提供的API endpoint包含RPC HTTP endpoint、Websocket endpoint、unix socket \(Unix\) 或 named pipe \(Windows\)之IPC endpoint可使用。

輸入

```
geth console
```

即可進入 Geth 的Javascript console

> 也可以先在A terminal輸入geth，然後開啟另一個terminal輸入geth attach，開啟console

![](/assets/234324234.png)

之後在這邊可以輸入相關指令，指令列表可參考

[https://github.com/ethereum/go-ethereum/wiki/Management-APIs\#list-of-management-apis](https://github.com/ethereum/go-ethereum/wiki/Management-APIs#list-of-management-apis)

#### 使用JSON-RPC

規格：[http://www.jsonrpc.org/](http://www.jsonrpc.org/)

Ethereum JSON-RPC 文件： [https://github.com/ethereum/wiki/wiki/JSON-RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC)

JSON-RPC，為一個輕量級無狀態的RPC Protocol，通常為一個會回覆JSON格式的Server，試著啟動Geth之後輸入以下

```
curl --data '{"method":"web3_clientVersion","params":[],"id":1,"jsonrpc":"2.0"}' \
-H "Content-Type: application/json" \
-X POST localhost:8545
```

> 因為指令較長所以分行，shell結尾的分行符號`\`前面要有一個空格，後面不可以有空格。

# Geth可用指令與參數

## 可用指令

輸入`geth help` 可以查看可以使用的指令與參數。

[https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options](https://www.gitbook.com/book/easonwang01/e/edit#)

```
   account     管理帳號，可用指令包含list, new, update, import

   attach      啟動JS的console，會連線到一個節點

   bug         會自動開啟 go-ethereum 在 Github 的 issue 頁面，可快速回報Bug

   console     啟動節點時，同時啟動JS的console

   copydb      從另外一個 chaindata 資料夾引入相關區塊鏈資料

   dump        取得特定區塊的相關資料，與console中的debug.dumpBlock()類似

   dumpconfig  顯示節點基本資料，例如DataDir, NetworkId, SyncMode, GasPrice等等

   export      輸出區塊鏈資料到檔案，例如： geth export <filename>

   import      引入區塊鏈檔案到節點中，例如： geth import <filename>

   init        初始化一個新的 genesis block

   js          執行特定 JavaScript 檔案，例如：echo "console.log(123)" >> test.js 然後 geth js ./test.js

   license     顯示相關 license 資料

   makecache   產生 ethash verification cache (測試使用)

   makedag     產生 ethash mining DAG (測試使用)

   monitor     顯示與監控相關節點資訊，可參考：
               https://github.com/ethereum/go-ethereum/wiki/Metrics-and-Monitoring

   removedb    移除節點區塊鏈資料

   version     取得Geth目前版本

   wallet      管理相關 Ethereum presale wallets，可參考：
               https://ethereum.stackexchange.com/questions/15918/what-does-presale-mean-in-ethereum
```

## 相關參數

Geth於啟動時可以在指令上加上 flag 參數，讓Geth使用不同的狀態執行。

e.g.

```
geth.exe --fast --cache 1024

// --fast 意思為只下載區塊鏈上的狀態而不下載所有區塊鏈上所有的資料，可以快速地進行同步，但只限於第一次同步時輸入
// --cache=1024 意思為設定電腦上的RAM在同步時的資料最大快取為1024MB
```

以下為部分較常用的指令 :

#### --testnet、--rinkeby 、--dev

```
--testnet      連線到 Ropsten network，共識方式為 proof-of-work 
--rinkeby      連線到 Rinkeby network，共識方式為 proof-of-authority 
--dev          連線到私有鏈，方便開發者快速開發，並且預設好一個帳號並擁有一定數量之Ether，共識方式為 proof-of-authority
```

#### --syncmode

其影響主要為同步區塊鏈之總大小不同。

> 以前的 --fast 與 --light 需要改為如下

```
--syncmode "fast" 
//  節點同步選項，可以選擇如 ("fast", "full", 或是 "light")
```

```
Full Sync: 同步所有 block headers 與 block bodies。

Fast Sync: 同步所有 block headers 與 block bodies。在同步到 current block - 1024 之前不會處理任何交易。

Light Sync: 只取得目前的區塊狀態，要驗證資料時必須傳送資料給其他節點。
```

#### --rpc、--ws、--ipc

--rpc

```
--rpc                   啟用 HTTP-RPC server ，預設只會啟用IPC
--rpcaddr <value>       設定 HTTP-RPC server 的監聽IP   
--rpcport <value>       設定 HTTP-RPC server 的監聽PORT 預設為 8545
--rpcapi <value>        設定可用的 HTTP-RPC interface  e.g. personal, admin
--rpccorsdomain <value> 設定可跨域存取的網址
```

-ws

即為Websocket

```
--ws                     啟用 WS-RPC server
--wsaddr <value>         設定WS-RPC server listening interface (預設為: "localhost")
--wsport <value>         設定WS-RPC server listening port (預設為: 8546)
--wsapi <value>          設定可用的 WS-RPC interface  e.g. personal, admin
--wsorigins <value>      設定可跨域存取的網址
```

--ipc

```
--ipcdisable          不啟用 IPC-RPC server ，預設為自動啟用
--ipcpath             IPC socket/pipe 之檔案名稱
```

> --port 可以改變節點監聽之port
>
> Ethereum預設有**listener \(TCP\) **與 **discovery \(UDP**\) 監聽在**30303，可使用--port改變預設阜號**

#### --datadir、--keystore

```
--datadir <資料夾路徑>
--keystore <資料夾路徑>
--networkid <數字>
```

--datadir可以指定區塊鏈存放之資料夾位置，如果沒指定的話，預設位置如下

```
Mac: ~/Library/Ethereum
Linux: ~/.ethereum
Windows: %APPDATA%\Ethereum
```

--keystore指定帳戶之金鑰存放位置，預設在datadir資料夾內

# 將Metamask的帳號import到Geth

我們時常會使用到Metamask的服務，但有時我們想讓帳號在Geth也可以使用，所以可以使用以下方法將之引入到Geth：

1.先到Metamask將Private key取出

![](/assets/螢幕快照 2018-01-12 上午9.17.50.png)

2.然後進入到Geth指令介面後輸入如下：

```
personal.importRawKey("剛才輸出的私鑰","設定一個密碼")
```

3.接著進入Geth 的 console輸入`eth.accounts` 即可看到引入的帳號。

