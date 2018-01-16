# 部署及執行合約

這邊我們會使用兩個工具

```
Remix IDE(http://remix.ethereum.org)
Mist     (https://github.com/ethereum/mist)
```

Remix IDE先前已經介紹過，這邊我們要來下載Mist，Mist是一個可以部署與執行智能合約的以太坊錢包。

Mist下載頁面：https://github.com/ethereum/mist/releases

我們剛才已經啟動一個私有鏈節點，這時我們可以直接開啟Mist，會自動連線到該節點，如果沒有的話可以用如下方式啟動，幫他指定要連線的RPC server位置。

OSX : 

```
/Applications/Ethereum\ Wallet.app/Contents/MacOS/Ethereum\ Wallet --rpc http://localhost:8545
```



