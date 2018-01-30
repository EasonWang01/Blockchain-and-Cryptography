# Raiden雷電網路

我們可以選擇直接下載，或自行編譯。

#### 下載：

[https://github.com/raiden-network/raiden/releases](https://github.com/raiden-network/raiden/releases)

#### 自行編譯：

需要安裝好以下程式：

1.python3

[https://www.python.org/downloads/](https://www.python.org/downloads/)

2.solc

[http://solidity.readthedocs.io/en/latest/installing-solidity.html](http://solidity.readthedocs.io/en/latest/installing-solidity.html)

> solc不能安裝npm版本，因為其command名稱為solcjs，之後會無法作用，必須安裝其他版本。

3.pyethapp

[https://github.com/ethereum/pyethapp/\#installation-on-ubuntudebian](https://github.com/ethereum/pyethapp/#installation-on-ubuntudebian)

然後

```
git clone https://github.com/raiden-network/raiden.git
cd raiden
pip install --upgrade -r requirements.txt
python setup.py develop
```

#### 同步節點

目前只能使用完全同步後的Ropsten測試網路來進行Raiden雷電網路的執行，輸入以下指令來進行節點同步。

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

接著我們啟動Raiden

```
raiden --keystore-path $HOME/Library/Application\ Support/io.parity.ethereum/keys/test
```

Parity的Ropsten鏈Key 通常會存在以下路徑：

```
Windows: %UserProfile%\AppData\Roaming\Parity\keys\test
macOS: $HOME/Library/Application\ Support/io.parity.ethereum/keys/test
Linux: ~/.local/share/io.parity.ethereum/keys/test
```

之後會進入如下畫面

![](/assets/螢幕快照 2018-01-30 下午10.32.19.png)

然後我們選擇一個在Parity創建的帳號：

![](/assets/螢幕快照 2018-01-30 下午10.32.48.png)

這時因為帳號裡面沒有餘額，我們必須要向測試鏈Ropsten 的 faucet 水龍頭拿取測試幣。

可到這個網站：[http://faucet.ropsten.be:3001/，然後填入地址](http://faucet.ropsten.be:3001/，然後填入地址)

![](/assets/螢幕快照 2018-01-30 下午10.35.40.png)之後確認我們的帳戶拿到測試後即可繼續進行

![](/assets/螢幕快照 2018-01-30 下午10.36.54.png)

之後選擇帳號，如果測試鏈還沒同步完成會出現如下畫面：

![](/assets/螢幕快照 2018-01-30 下午11.08.48.png)

同步好後會出現：

![](/assets/螢幕快照 2018-01-30 下午11.09.27.png)

## 開始使用

Raiden 有一系列的 RESTful API 可以供使用者對其Channel進行相關操作。

---

#### 安裝時可能產生的Issue：

[https://github.com/raiden-network/raiden/issues/1235](https://github.com/raiden-network/raiden/issues/1235)

[https://github.com/ethereum/pyethapp/issues/209\#issuecomment-308466232](https://github.com/ethereum/pyethapp/issues/209#issuecomment-308466232)

