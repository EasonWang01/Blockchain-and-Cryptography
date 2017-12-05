# Bitcoin P2P節點連線



## 節點搜尋

在第一次與其他節點連接時，會先去搜尋寫在原始碼裡面的[DNS seeds](https://bitcoin.org/en/glossary/dns-seed)，之後節點啟動後會預設先去找DNS seed server尋求目前可用的節點來同步資料。

> 成為DNS Seed Server的要件![](/assets/234.png)https://github.com/bitcoin/bitcoin/blob/57b34599b2deb179ff1bd97ffeab91ec9f904d85/doc/dnsseed-policy.md





