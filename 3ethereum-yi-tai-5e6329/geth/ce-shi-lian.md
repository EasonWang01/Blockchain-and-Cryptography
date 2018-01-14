# --dev

--dev指令可以方便讓開發人員進行測試的指令，使用後會創建一個預設帳號，裡面已經擁有Ether，並且此鏈為POA\(Proof of Authority\)，所以沒有miner.start\(\)相關指令，而dev鏈發生交易後會自動加入區塊。

```
geth --datadir ./.ethereum/devnet --dev console
```

之後我們查看預設帳號的餘額

```
eth.getBalance(eth.accounts[0])
```

![](/assets/螢幕快照 2018-01-12 上午8.53.30.png)然後我們新創建一個帳號

```
personal.newAccount()
```

接著轉帳

```
eth.sendTransaction({from:eth.coinbase, to:eth.accounts[1], value: web3.toWei(0.05, "ether")})
```

之後不用挖礦及會自動生效

![](/assets/螢幕快照 2018-01-12 上午8.54.52.png)

## Testrpc

使用以下指令安裝

```
npm install -g ganache-cli
```

> 在以前的安裝方式為如下，但現在ethereumjs-testrpc已經整合到ganach裡面
>
> ```
> npm install -g ethereumjs-testrpc
> ```

https://github.com/trufflesuite/ganache-cli

啟動

```
ganache-cli
```

> ganache也有圖形化介面程式可下載http://truffleframework.com/ganache/

Testrpc提供一個快速的測試環境，啟動時會自動創建十組帳號且裡面都有一定量的Ether可以做測試、每次有交易後會自動挖礦，但是鏈的資料是暫存在記憶體而不是在檔案中，所以Testrpc關掉之後，所有區塊鏈的資料都會消失。

![](/assets/0921.png)

