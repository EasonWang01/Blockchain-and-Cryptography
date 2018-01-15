# Bitcoin P2P節點連線

比特幣的網路是 P2P 的，類似於 webtorrent 都是沒有中心化的server，節點之間互相傳遞區塊訊息，並且提供JSON-RPC之API請求查詢服務。

> [https://bitcoin.org/en/developer-guide\#p2p-network](https://bitcoin.org/en/developer-guide#p2p-network)

比特幣的全節點\([mainnet](https://www.gitbook.com/book/easonwang01/e/edit#)\)預設跑在8333的PORT上，而[testnet](https://www.gitbook.com/book/easonwang01/e/edit#)預設跑在18333上。

## Bitcoin Protocol

我們可以使用『 Wireshark 』軟體來監聽節點間的封包

![](/assets/螢幕快照 2017-12-06 下午9.19.50.png)

> 比特幣的封包傳遞時是使用ＴＣＰ連線，其protocal部分類似如下
>
> ![](/assets/螢幕快照 2017-12-06 下午9.11.20.png)
>
> 紅色箭頭部分為每個比特幣節點的網路封包都會包含的段落（Message Headers），而黃色箭頭部分則為根據該封包的Command Name欄位，擁有不同的message欄位

## 節點搜尋

在第一次與其他節點連接之前，會先去搜尋寫在原始碼裡面的[DNS seeds](https://bitcoin.org/en/glossary/dns-seed)，之後節點啟動後會預設先去找DNS seed server尋求目前可用的節點來同步資料。

> 成為DNS Seed Server的要件![](/assets/234.png)[https://github.com/bitcoin/bitcoin/blob/57b34599b2deb179ff1bd97ffeab91ec9f904d85/doc/dnsseed-policy.md](https://github.com/bitcoin/bitcoin/blob/57b34599b2deb179ff1bd97ffeab91ec9f904d85/doc/dnsseed-policy.md)

目前寫在原始碼的DNS Seed![](/assets/9876.png)[https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/chainparams.cpp](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/chainparams.cpp)

當節點向DNS Seed 發出請求後，其會返回多個目前可用來同步的節點IP，之後會把可用節點記錄在本地料庫中，避免每次啟動都發送DNS請求

> 可使用nslookup或dig發出DNS query請求

1.使用nslookup指令

![](/assets/98798.png)

2.使用Dig指令![](/assets/螢幕快照 2017-12-06 下午9.38.59.png)3.使用Node.js來查找

```js
const dns = require('dns');
const options = {
  family: 4,
  hints: dns.ADDRCONFIG | dns.V4MAPPED,
};

options.all = true;
dns.lookup('seed.bitcoin.sipa.be', options, (err, addresses) => {
 console.log('addresses: %j', addresses);
 console.log(addresses[0].address); //返回的IP中的第一個
});
```

## 



