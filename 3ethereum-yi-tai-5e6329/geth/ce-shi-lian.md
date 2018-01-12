

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

