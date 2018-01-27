# Ethereum 測試鏈

以太坊一開始的測試鏈名為 『 Morden testnet 』 從 2015年7月一直到 2016年11月，但因為在此鏈上很長的同步時間，以及一些節點共識問題的不穩定，後來誕生了『 Ropsten testnet 』。

『 Ropsten testnet 』成功的執行到了2017年2月，但後來還是因為節點的算力不足以及被濫用導致gas

limits 上升到 9 billion \(原先為 4.7 million\)。

後來『 Parity 』團隊想出了一半辦法，就是使用POA \( Proof-of-Authority \)，於是創造了『 Kovan testnet 』，但是Kovan只能給

『 Parity 』客戶端使用。

最後在『 go-ethereum 』團隊誕生了一個新的測試鏈，名為『 Rinkeby 』也是使用POA \( Proof-of-Authority \)的共識系統。  
有關其POA的protocol可參考：[https://github.com/ethereum/EIPs/issues/225](https://github.com/ethereum/EIPs/issues/225)

---

## 連線

最常使用的節點軟體為Geth或是Parity，加入以下Flag即可連線到對應的測試鏈。

1.連線到 Ropsten network

```
geth --testnet
parity --chain ropsten
```

> 有時無法sync，輸入eth.syncing時返回false，可以手動指定其他 enode
>
> ```
> enode://94c15d1b9e2fe7ce56e458b9a3b672ef11894ddedd0c6f247e0f1d3487f52b66208fb4aeb8179fce6e3a749ea93ed147c37976d67af557508d199d9594c35f09@192.81.208.223:30303
> ```

2.連線到 Rinkeby network 目前只能使用Geth連線

```
geth --rinkeby
```

3.連線到 Kovan testnet 目前只能使用Parity連線。

```
parity --chain kovan
```

> 如果無法sync，可以指定要sync的bootnode :
>
> ```
> --bootnodes enode://YOU_BOOT_NODE_ID_HERE@127.0.0.1:30303
> ```

## 相關網站

[https://testnet.etherscan.io/](https://www.gitbook.com/book/easonwang01/e/edit#)

#### Ropsten

查看區塊鏈狀態：[https://ropsten.etherscan.io/](https://ropsten.etherscan.io/)

GIthub頁面：[https://github.com/ethereum/ropsten](https://github.com/ethereum/ropsten)

取得測試幣：[http://faucet.ropsten.be:3001/](http://faucet.ropsten.be:3001/)

#### Koven

查看區塊鏈狀態：[https://kovan.etherscan.io/](https://kovan.etherscan.io/)

GIthub頁面：[https://github.com/kovan-testnet](https://github.com/kovan-testnet)

取得測試幣：[https://github.com/kovan-testnet/faucet](https://github.com/kovan-testnet/faucet)

#### Rinkeby

查看區塊鏈狀態：[https://www.rinkeby.io/\#stats](https://www.rinkeby.io/#stats)

取得測試幣：[https://faucet.rinkeby.io/](https://faucet.rinkeby.io/)

## 名稱由來

```
Morden:  位於 London 的地鐵站  
https://tfl.gov.uk/hub/stop/940GZZLUMDN/morden-underground-station

Ropsten: 位於 Stockholm 的地鐵站 
https://en.wikipedia.org/wiki/Ropsten_metro_station

Kovan: 位於 Singapore的 地鐵站
https://en.wikipedia.org/wiki/Kovan_MRT_station

Rinkeby: 位於 Stockholm 的地鐵站
https://en.wikipedia.org/wiki/Rinkeby_metro_station
```

參考資料：

[https://www.ethnews.com/ropsten-to-kovan-to-rinkeby-ethereums-testnet-troubles](https://www.ethnews.com/ropsten-to-kovan-to-rinkeby-ethereums-testnet-troubles)

[https://ethereum.stackexchange.com/questions/29194/why-are-the-rinkeby-and-ropsten-testnets-named-after-swedish-locations](https://ethereum.stackexchange.com/questions/29194/why-are-the-rinkeby-and-ropsten-testnets-named-after-swedish-locations)

[https://ethereum.stackexchange.com/questions/27048/comparison-of-the-different-testnets/30072\#30072](https://ethereum.stackexchange.com/questions/27048/comparison-of-the-different-testnets/30072#30072)

