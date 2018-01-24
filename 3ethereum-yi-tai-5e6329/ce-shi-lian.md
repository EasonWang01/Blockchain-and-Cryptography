# Ethereum 測試鏈

以太坊一開始的測試鏈名為 『 Morden testnet 』 從 2015年7月一直到 2016年11月，但因為在此鏈上很長的同步時間，以及一些節點共識問題的不穩定，後來誕生了『 Ropsten testnet 』。

『 Ropsten testnet 』成功的執行到了2017年2月，但後來還是因為節點的算力不足以及被濫用導致gas

limits 上升到 9 billion \(原先為 4.7 million\)。

後來『 Parity 』團隊想出了一半辦法，就是使用POA \( Proof-of-Authority \)，於是創造了『 Kovan testnet 』，但是Kovan只能給

『 Parity 』客戶端使用。

最後誕生了一個可以給任何客戶端使用的測試鏈，名為『 Rinkeby 』也是使用POA \( Proof-of-Authority \)的共識系統。  
有關其POA的protocol可參考：[https://github.com/ethereum/EIPs/issues/225](https://github.com/ethereum/EIPs/issues/225)

---

#### 連線

使用Geth加入以下Flag即可連線到對應的測試鏈。

1.連線到 Ropsten network

```
geth --testnet   
```

2.連線到 Rinkeby network

```
geth --rinkeby   
```

3.Kovan testnet 只能使用Parity連線。

```
parity --chain kovan
```

參考資料：

[https://www.ethnews.com/ropsten-to-kovan-to-rinkeby-ethereums-testnet-troubles](https://www.ethnews.com/ropsten-to-kovan-to-rinkeby-ethereums-testnet-troubles)

