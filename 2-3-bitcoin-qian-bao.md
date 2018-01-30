# Bitcoin 錢包

比特幣錢包種類包含有網頁版、桌面版、手機APP，還有適合開發者使用的Command Line介面錢包，紙本錢包、USB硬體錢包以及各個交易所保管的錢包等等，但使用上之安全性需要讀者自行評估。

每個比特幣錢包裡面可以包含許多比特幣地址且均有對應的私鑰，以下我們將用官方的錢包來講解。

我們可以先打開先前提到的`Bitcoin-qt`視窗程式，然後點選

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

> 大家可能會想：地址應該是由私鑰所產生，但怎麼這裡是輸入地址取得私鑰呢？原因是在我們產生地址時他同時已經將對應的私鑰存在電腦裡了，所以`dumpprivkey`僅僅是把對應地址的私鑰讀取出來而已。

3.獲取地址內餘額

> 記得要將電腦本地的區塊鏈資料完整同步，之後才能得到正確的餘額。

```
getreceivedbyaddress 填入地址
```

![](/assets/newadd2.png)

4.取得節點相關資料

> 這裡一樣要完整同步後才可得到正確資料。

```
getinfo
```

![](/assets/newadd3.png)

## 有關其他可下載的用戶端錢包

除了官方的錢包之外，還有其他第三方所製作的錢包程式可以使用。

### Full node Client

Bitcoin-Core

```
https://github.com/bitcoin/bitcoin
```

btcd

```
https://github.com/btcsuite/btcd
```

libbitcoin-server

```
https://github.com/libbitcoin/libbitcoin-server
```

### Thin client

比較輕量的客戶端，只會下載區塊頭與部分區塊鏈上的資料，也稱為SPV。

> As Satoshi writes, "\[the thin client\] can't check the transaction for himself, but by linking it to a place in the chain, he can see that a network node has accepted it, and blocks added after it further confirm the network has accepted it." If we take "X" to be the "number of blocks added after it", then a thin client essentially trusts that a transaction X blocks deep will be costly to forge.

1.bitcoind

可以加上指令跑較輕量的client大約只需2GB的儲存空間，啟動節點時加上以下指令，N是分配給raw block 與 undo data的MiB \( 附註1 \) 大小。

```
prune=<N>
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

```
https://electrum.org/#home
```

[https://bitzuma.com/posts/a-beginners-guide-to-the-electrum-bitcoin-wallet/](https://bitzuma.com/posts/a-beginners-guide-to-the-electrum-bitcoin-wallet/)

> 以下是一篇關於SPV節點安全性的文章，探討用SPV節點在安全性方面的考量。  
> [https://medium.com/@jonaldfyookball/why-every-bitcoin-user-should-understand-spv-security-520d1d45e0b9](https://medium.com/@jonaldfyookball/why-every-bitcoin-user-should-understand-spv-security-520d1d45e0b9)

我們也可以直接到以下網站，查看目前可用的第三方錢包並進行安裝。可選擇電腦版、硬體版、手機與網頁版

[https://bitcoin.org/en/wallets/hardware/](https://bitcoin.org/en/wallets/desktop/windows/)

![](/assets/螢幕快照 2017-11-09 下午10.39.05.png)

---

附註 :

1.Mib \( Mebibyte \)

[https://zh.wikipedia.org/wiki/Mebibyte](https://zh.wikipedia.org/wiki/Mebibyte)

