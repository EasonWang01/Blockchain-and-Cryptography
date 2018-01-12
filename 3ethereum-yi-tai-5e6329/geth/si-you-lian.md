# Ethereum私有鏈

這邊我們會自行建立一個genesis block創世區塊，然後啟動幾個私有鏈之節點互相連線，並且擁有相同的私有區塊鏈。

1.新增帳號

我們先新建一個帳號，並且指定存放在特定資料夾中

```
geth account new --datadir ./ethPrivate
```

> 之後在ethPrivate資料夾中會新增一個keystore資料夾，裡面存放著帳戶金鑰

![](/assets/321.png)2.新增Genesis block 檔案

genesis.json

```json
{
    "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "difficulty": "200000000",
    "gasLimit": "2100000",
    "alloc": {
        "4d079eff6af98e4980e7263012a42b08f5b2691b": { "balance": "300000" }
    }
}
```

> alloc欄位填上剛才新增的帳戶，可以預先分配Ether進去

在來把檔案存到 ./ethPrivate 裡面，然後在Terminal輸入如下 :

```
geth --datadir ./ethPrivate init ./ethPrivate/genesis.json
```

![](/assets/9203.png)現在./ethPrivate資料夾結構如下

![](/assets/3453455.png)

之後回到Terminal，然後輸入如下指令啟動節點:

```
geth --datadir ./ethPrivate --networkid 15 console
```

然後輸入以下即可看到預先分配的Ether

![](/assets/903.png)

