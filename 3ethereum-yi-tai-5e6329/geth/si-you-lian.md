# Ethereum 私有鏈

以太坊中可以建立私有鏈，私有鏈的資料與主鏈不相關，鏈上的資料都是獨立於主鏈的。

這邊我們會自行建立一個創世區塊（Genesis block），並啟動幾個私有鏈節點互相連線，讓其區塊鏈資料進行同步。

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
    "difficulty": "0x40",
    "gasLimit": "2100000",
    "alloc": {
        "4d079eff6af98e4980e7263012a42b08f5b2691b": { "balance": "300000" }
    }
}
```

> alloc欄位填上剛才新增的帳戶，可以預先分配Ether進去

再來把`genesis.json`檔案存到 ./ethPrivate 裡面，然後在Terminal輸入如下指令來初始化 :

```
geth --datadir ./ethPrivate init ./ethPrivate/genesis.json
```

![](/assets/9203.png)現在./ethPrivate資料夾結構如下

![](/assets/3453455.png)

回到Terminal，輸入如下指令啟動節點:

```
geth --datadir ./ethPrivate --nodiscover --networkid 15 console
```

啟動後輸入以下指令，即可看到預先分配的Ether在 帳戶0 的餘額中。

![](/assets/903.png)

## 新增節點

我們接著初始化另外一個節點，先在另一個資料夾內創建一個帳戶

```
geth account new --datadir ./ethPrivate_01
```

然後把剛才的genesis.json存入該節點，之後一樣初始化區塊鏈

> 記得兩個genesis.json要相同。

```
geth --datadir ./ethPrivate_01 init ./ethPrivate_01/genesis.json
```

啟動該節點

```
geth --datadir ./ethPrivate_01 --nodiscover --networkid 15 --ipcpath ./.ipc/geth1.ipc --port 30304 console
```

> 1.兩個節點要連線的關鍵點是genesis.json創世區塊要相同，以及networkid要相同（註一）。
>
> 2.每個節點監聽的 Port 要不同。
>
> 3.每個節點的IPC path或RPC port要不同。
>
> 4.--nodiscover 是避免節點自己尋找其他節點連線。

## 讓兩節點連線

先在其中一個節點輸入

```
admin.nodeInfo
```

然後把enode部分複製

![](/assets/900.png)在另外一個節點輸入`admin.addPeer` 指令，如下：

> 括號內填入剛才上面複製的enode URL括號內填入

```
admin.addPeer("enode://d86c9070b332da1f35690310f15dd728e1d65f749e61e00f1da412a86b483c6a536cbb19e64ef3fd43781d5802dbe3d05c83a882c342d2505fb6686514323326@[::]:30304?discport=0")
```

然後輸入`admin.peers`確認目前已加入的節點：

![](/assets/9021.png)

> 即可看到兩節點現在已經互相連線

# 進行挖礦

先設定挖礦後的獎勵要到哪一個帳號

```
miner.setEtherbase(eth.accounts[0])
```

開始挖礦

```
miner.start(1)
```

> 括號內數字代表用幾顆CPU核心來挖礦

執行後即可看到兩節點出現訊息，而挖到區塊後帳號內的餘額會增加。

![](/assets/201.png)可使用以下指令結束挖礦

```
miner.stop()
```

## 發送交易

需要先解鎖要發送交易的帳號

```
personal.unlockAccount(eth.accounts[0])
```

> to 的值記得要填入要轉帳過去的帳號

```
eth.sendTransaction({from:eth.accounts[0], to: "0x0bc1d752dfe1e2595017738b5a944a4e7faa4b74", value: web3.toWei(0.05777, "ether")})
```

之後要進行挖礦，交易才能被納入區塊

```
miner.start(1)
```

交易納入區塊後可以輸入以下指令即可看到目前帳戶餘額。

```
eth.getBalance(eth.account[0])
```

![](/assets/9123.png)

---

註一：

Network ID：用來識別不同網路的號碼

以下為目前已被使用的ID

```json
0: Olympic; Ethereum public pre-release testnet

1: Frontier; Homestead, Metropolis, the Ethereum public main network

1: Classic; The (un)forked public Ethereum Classic main network, chain ID 61

1: Expanse; An alternative Ethereum implementation, chain ID 2

2: Morden; The public Ethereum testnet, now Ethereum Classic testnet

3: Ropsten; The public cross-client Ethereum testnet

4: Rinkeby: The public Geth Ethereum testnet

42: Kovan; The public Parity Ethereum testnet

77: Sokol; The public POA testnet

99: POA; The public Proof of Authority Ethereum network

7762959: Musicoin; The music blockchain
```

> [https://ethereum.stackexchange.com/a/17101](https://ethereum.stackexchange.com/a/17101)



