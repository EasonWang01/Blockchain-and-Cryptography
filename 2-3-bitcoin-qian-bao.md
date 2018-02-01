# Bitcoin 錢包

比特幣錢包種類包含有網頁版、桌面版、手機APP，還有適合開發者使用的Command Line介面錢包，紙本錢包、USB硬體錢包等等，但使用上之安全性需要讀者自行評估。

每個比特幣錢包裡面可以包含許多比特幣地址與對應的私鑰，以下我們將用官方的錢包來講解。

打開先前提到的`Bitcoin-qt`視窗程式，然後點選

```
說明 -> 除錯視窗 -> 主控台
```

1.我們先產生一個地址

```
getnewaddress
```

![](/assets/newadd.png)

2.然後取得該地址的私鑰

```
dumpprivkey 填入地址
```

![](/assets/newadd1.png)

> 大家可能會想：地址應該是由私鑰所產生，但怎麼這裡是輸入地址取得私鑰呢？原因是在我們產生地址時，程式同時已經將對應的私鑰存在電腦裡了，所以`dumpprivkey`僅僅是把地址對應的私鑰讀取出來而已。

3.獲取地址內餘額

> 記得要將電腦本地的區塊鏈資料完整同步，之後才能得到正確的餘額。

```
getreceivedbyaddress 填入地址
```

![](/assets/newadd2.png)

4.取得節點相關資料

> 以下指令必須要完整同步後才可得到正確資料。

```
getinfo
```

![](/assets/newadd3.png)

## 有關其他可下載的用戶端錢包

除了官方的節點錢包之外，還有其他第三方所製作的節點錢包程式可以使用。

### Full node Client

全節點，會下載所有區塊鏈資料到本地端，需求較大的儲存空間。

1.Bitcoin-Core

```
https://github.com/bitcoin/bitcoin
```

2.btcd

```
https://github.com/btcsuite/btcd
```

3.libbitcoin-server

```
https://github.com/libbitcoin/libbitcoin-server
```

### Thin client

比較輕量的用戶端，只會下載區塊頭與部分區塊鏈上的資料，也稱為SPV節點。

1.Bitcoin-Core

Bitcoin-Core也可以當作輕量級用戶端，加上以下指令跑較輕量的client大約只需2GB的儲存空間，啟動節點時加上以下指令。

> N是分配給raw block 與 undo data的MiB 大小\( 附註1 \)。
>
> 記得只有在節點上未進行任何同步時才可輸入，如果先前執行時沒有輸入`prune=<N>`，則之後無法用此方法同步。

```
prune=<N>

例如：./bitcoind -prune=560
```

> 可參考 : [https://github.com/bitcoin/bitcoin/blob/v0.12.0/doc/release-notes.md\#wallet-pruning](https://github.com/bitcoin/bitcoin/blob/v0.12.0/doc/release-notes.md#wallet-pruning)

2.bitcoinj

```
https://bitcoinj.github.io/
```

3.picocoin

```
https://github.com/jgarzik/picocoin/
```

4.Electrum

[https://bitzuma.com/posts/a-beginners-guide-to-the-electrum-bitcoin-wallet/](https://www.gitbook.com/book/easonwang01/e/edit#)

```
https://electrum.org/#home
```

但是SPV節點由於不是自行下載所有區塊鏈上的資料來進行交易驗證，會產生一些安全性議題。

> As Satoshi writes, the thin client can't check the transaction for himself, but by linking it to a place in the chain, he can see that a network node has accepted it, and blocks added after it further confirm the network has accepted it." If we take "X" to be the "number of blocks added after it", then a thin client essentially trusts that a transaction X blocks deep will be costly to forge.

[https://en.bitcoin.it/wiki/Thin\_Client\_Security](https://en.bitcoin.it/wiki/Thin_Client_Security)

> 以下是幾篇關於SPV節點安全性的文章，探討用SPV節點在安全性方面的考量。  
> [https://medium.com/@jonaldfyookball/why-every-bitcoin-user-should-understand-spv-security-520d1d45e0b9](https://medium.com/@jonaldfyookball/why-every-bitcoin-user-should-understand-spv-security-520d1d45e0b9)
>
> https://jonasnick.github.io/blog/2015/02/12/privacy-in-bitcoinj/

### 錢包列表：

我們也可以直接到以下網站，查看目前可用的第三方錢包並進行安裝。可選擇電腦版、硬體版、手機與網頁版

[https://bitcoin.org/en/wallets/hardware/](https://bitcoin.org/en/wallets/desktop/windows/)

![](/assets/螢幕快照 2017-11-09 下午10.39.05.png)

---

附註 :

1.Mib \( Mebibyte \)

[https://zh.wikipedia.org/wiki/Mebibyte](https://zh.wikipedia.org/wiki/Mebibyte)

