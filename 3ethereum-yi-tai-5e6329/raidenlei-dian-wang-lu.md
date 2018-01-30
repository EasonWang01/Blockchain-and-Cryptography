# Raiden雷電網路

我們可以選擇直接下載，或自行編譯。

#### 下載：

[https://github.com/raiden-network/raiden/releases](https://github.com/raiden-network/raiden/releases)

#### 自行編譯：

需要裝好：

[http://solidity.readthedocs.io/en/latest/installing-solidity.html](http://solidity.readthedocs.io/en/latest/installing-solidity.html)

[https://github.com/ethereum/pyethapp/\#installation-on-ubuntudebian](https://github.com/ethereum/pyethapp/#installation-on-ubuntudebian)

然後

```

```

目前只能使用完全同步後的Ropsten測試網路來進行Raiden雷電網路的執行。

```
parity --chain ropsten --bootnodes \
"enode://94c15d1b9e2fe7ce56e458b9a3b672ef11894ddedd0c6f247e0f1d3487f52b66208fb4aeb8\
179fce6e3a749ea93ed147c37976d67af557508d199d9594c35f09@192.81.208.223:30303,\
enode://20c9ad97c081d63397d7b685a412227a40e23c8bdc6688c6f37e97cfbc22d2b4d1db1\
510d8f61e6a8866ad7f0e17c02b14182d37ea7c3c8b9c2683aeb6b733a1@52.169.14.227:30303,\
enode://6ce05930c72abc632c58e2e4324f7c7ea478cec0ed4fa2528982cf34483094e9cbc9216e7a\
a349691242576d552a2a56aaeae426c5303ded677ce455ba1acd9d@13.84.180.240:30303"
```

我們使用Parity進行Ropsten測試網路的sync，同步後的畫面如下。

![](/assets/螢幕快照 2018-01-29 上午12.15.43.png)

s

---

#### 安裝時可能產生的Issue：

[https://github.com/raiden-network/raiden/issues/1235](https://github.com/raiden-network/raiden/issues/1235)

[https://github.com/ethereum/pyethapp/issues/209\#issuecomment-308466232](https://github.com/ethereum/pyethapp/issues/209#issuecomment-308466232)

