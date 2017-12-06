# Bitcoin P2P節點連線

> https://bitcoin.org/en/developer-guide\#p2p-network

比特幣的全節點\([mainnet](https://www.gitbook.com/book/easonwang01/e/edit#)\)預設跑在8333的PORT上，而[testnet](https://www.gitbook.com/book/easonwang01/e/edit#)預設跑在18333上。

## 節點搜尋

在第一次與其他節點連接時，會先去搜尋寫在原始碼裡面的[DNS seeds](https://bitcoin.org/en/glossary/dns-seed)，之後節點啟動後會預設先去找DNS seed server尋求目前可用的節點來同步資料。

> 成為DNS Seed Server的要件![](/assets/234.png)[https://github.com/bitcoin/bitcoin/blob/57b34599b2deb179ff1bd97ffeab91ec9f904d85/doc/dnsseed-policy.md](https://github.com/bitcoin/bitcoin/blob/57b34599b2deb179ff1bd97ffeab91ec9f904d85/doc/dnsseed-policy.md)

目前寫在原始碼的DNS Seed![](/assets/9876.png)[https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/chainparams.cpp](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/chainparams.cpp)

當節點向DNS Seed 發出請求後，其會返回多個目前可用來同步的節點IP，之後會把可用節點記錄在本地料庫中，避免每次啟動都發送DNS請求

> 可使用nslookup或dig發出DNS query請求

![](/assets/98798.png)

