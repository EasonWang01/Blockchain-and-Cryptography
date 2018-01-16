# 2-4 Bitcoin 錢包

每個比特幣錢包裡面可以包含許多比特幣地址且均有對應的私鑰。

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
dumpprivkey
```

![](/assets/newadd1.png)

> 大家可能會想：地址應該是由私鑰所產生，但怎麼這裡是輸入地址取得私鑰呢？原因是在我們產生地址時他已經他私鑰存在電腦裡了，所以`dumpprivkey`僅僅是把對應地址的私鑰讀取出來而已。

3.獲取地址內餘額

> 記得要將電腦本地的區塊鏈資料完整同步，之後才能得到正確的餘額。

```
getreceivedbyaddress
```

![](/assets/newadd2.png)

4.取得其他資料

> 這裡一樣要完整同步後才可得到正確資料。

```
getinfo
```

![](/assets/newadd3.png)

## 有關其他可下載的客戶端錢包

### Full node Client

Bitcoin-qt

```
https://bitcoin.org/en/full-node
```

btcd

```
https://github.com/btcsuite/btcd
```

libbitcoin-server

```
https://en.bitcoin.it/wiki/Libbitcoin
```

### Thin client

比較輕量的客戶端，只會下載區塊頭與部分區塊鏈上的資料  
也稱為SPV

> As Satoshi writes, "\[the thin client\] can't check the transaction for himself, but by linking it to a place in the chain, he can see that a network node has accepted it, and blocks added after it further confirm the network has accepted it." If we take "X" to be the "number of blocks added after it", then a thin client essentially trusts that a transaction X blocks deep will be costly to forge.

bitcoind可以加上指令跑較輕量的client大約只需2GB的儲存空間，啟動節點時加上以下指令，N是分配給raw block 與 undo data的MiB大小。

```
prune=<N>
```

> 可參考 : [https://github.com/bitcoin/bitcoin/blob/v0.12.0/doc/release-notes.md\#wallet-pruning](https://github.com/bitcoin/bitcoin/blob/v0.12.0/doc/release-notes.md#wallet-pruning)

bitcoinj

```
https://en.bitcoin.it/wiki/Bitcoinj
```

picocoin

```
https://github.com/jgarzik/picocoin/
```

Electrum

```
https://electrum.org/#home
```

[https://bitzuma.com/posts/a-beginners-guide-to-the-electrum-bitcoin-wallet/](https://bitzuma.com/posts/a-beginners-guide-to-the-electrum-bitcoin-wallet/)

> 一篇關於SPV安全性的文章  
> [https://medium.com/@jonaldfyookball/why-every-bitcoin-user-should-understand-spv-security-520d1d45e0b9](https://medium.com/@jonaldfyookball/why-every-bitcoin-user-should-understand-spv-security-520d1d45e0b9)

我們也可以直接到以下網站，查看目前可用的第三方錢包並進行安裝。可選擇電腦版、硬體版、手機與網頁版

[https://bitcoin.org/en/wallets/hardware/](https://bitcoin.org/en/wallets/desktop/windows/)

![](/assets/螢幕快照 2017-11-09 下午10.39.05.png)

---

附註 :

1.Mib \( Mebibyte \)

https://zh.wikipedia.org/wiki/Mebibyte

