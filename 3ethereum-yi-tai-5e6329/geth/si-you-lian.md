# Ethereum私有鏈

這邊我們會自行建立一個genesis block創世區塊，然後啟動幾個私有鏈之節點互相連線，並且擁有相同的私有區塊鏈。

1.新增帳號

我們先新建一個帳號，並且指定存放在特定資料夾中

```
geth account new --datadir ./ethPrivate
```

> 之後在ethPrivate資料夾中會新增一個keystore資料夾，裡面存放帳戶金鑰



