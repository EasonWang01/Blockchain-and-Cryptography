# Raiden雷電網路

雷電網路的作用類似於閃電網路，都是用來作為小額支付在區塊鏈上的解決方案。

目前[Sharding](https://github.com/ethereum/sharding)與[Plasma](https://plasma.io/)等區塊鏈相關架構之解決方案都是為了解決區塊鏈上交易認證速度緩慢以及交易堵塞等問題，而目前雷電網路為一個較為完整方案。

雷電網路現階段只能在Ropsten測試鏈測試。

#### 下載：

我們可以選擇直接下載，或自行編譯。

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

安裝好以上基本需求後，可以開始安裝 raiden-network 程式

> 因為目前還在PoC階段，所以是獨立的程式

```
git clone https://github.com/raiden-network/raiden.git
cd raiden
pip install --upgrade -r requirements.txt
python setup.py develop
```

#### 同步節點

目前只能使用完全同步後的Ropsten測試網路來進行Raiden雷電網路的執行，輸入以下指令來進行Ropsten測試網路的節點同步。

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

> --keystore-path 用來指定要使用哪個keys目錄。
>
> 在Parity的Ropsten鏈中，帳號金鑰通常會存在以下路徑：
>
> ```
> Windows: %UserProfile%\AppData\Roaming\Parity\keys\test
> macOS: $HOME/Library/Application\ Support/io.parity.ethereum/keys/test
> Linux: ~/.local/share/io.parity.ethereum/keys/test
> ```

啟動後會進入如下畫面

![](/assets/螢幕快照 2018-01-30 下午10.32.19.png)

然後我們選擇一個先前在Parity創建的帳號：

![](/assets/螢幕快照 2018-01-30 下午10.32.48.png)

這時因為帳號裡面沒有餘額，我們必須要向測試鏈Ropsten 的 faucet 水龍頭拿取測試幣。

可到這個網站：[http://faucet.ropsten.be:3001/  ](http://faucet.ropsten.be:3001/，然後填入地址)

然後在下方填入地址

![](/assets/螢幕快照 2018-01-30 下午10.35.40.png)之後確認我們的帳戶拿到測試幣後即可繼續進行

![](/assets/螢幕快照 2018-01-30 下午10.36.54.png)

接著回到Terminal選擇帳號，如果測試鏈還沒同步完成會出現如下畫面：

![](/assets/螢幕快照 2018-01-30 下午11.08.48.png)

同步好後會出現以下訊息，並且啟動RPC Server在5001 PORT：

![](/assets/螢幕快照 2018-01-30 下午11.09.27.png)

## 開始使用

Raiden 有一系列的 RESTful API 可以供使用者對其Channel進行相關操作。

[https://raiden-network.readthedocs.io/en/stable/rest\_api.html](https://raiden-network.readthedocs.io/en/stable/rest_api.html)

我們首先輸入以下，如果回傳與我們剛才輸入相同的地址，及代表Raiden正確運行：

```
curl -s http://127.0.0.1:5001/api/1/address
```

> Raiden收到後也會產生Log

![](/assets/螢幕快照 2018-01-30 下午11.25.19.png)

#### 註冊Token

假設我們在鏈上有一個ERC20 Token，地址為：0xC73cE6eA79CC86CC720303ECf2fa5aF8df99fe87

我們使用以下指令發出PUT請求，來註冊此地址的Token

```
curl -X PUT  http://127.0.0.1:5001/api/1/tokens/0xC73cE6eA79CC86CC720303ECf2fa5aF8df99fe87
```

![](/assets/螢幕快照 2018-01-31 上午12.10.25.png)

> 註冊後會回傳`{"channel_manager_address": "0x2ee7e1e036f5090387feddc87b39367356a690c0"}`

註冊後查看Token list

![](/assets/螢幕快照 2018-01-31 上午12.10.59.png)

#### 開啟Channel

要跟其他Raiden節點開啟Channel需要以下四件事情：

1.Token 合約的地址

2.另一個Raiden節點的地址

3.選擇要預存入Channel的金額

4.選擇Timeout時間

可以使用如下開啟

```
PUT /api/1/channels

加上的Payload如下:

{
    "partner_address": "0x61c808d82a3ac53231750dadc13c777b59310bd9",
    "token_address": "0x9aBa529db3FF2D8409A1da4C9eB148879b046700",
    "balance": 1337,
    "settle_timeout": 600
}
```

我們可用Postman發出請求，類似如下：

![](/assets/螢幕快照 2018-01-31 上午10.30.28.png)

#### 存錢到Channel

```
PATCH /api/1/channels/填上channel地址

{
    "balance": 10
}
```

#### 直接連線到Token network並存錢

```
PUT /api/1/connections/填上Token合約地址

{
    "funds": 2000
}
```

> This will automatically connect to and open channels with three random peers in the token network, with 20% of the funds deposited to each channel. Furthermore it will leave 40% of the funds initially unassigned
>
> 這會自動開啟Channel並且隨機連線到三個相關節點，給每個節點存各20%的前，然後剩下40%為待分配。

#### 離開Token network

```
DELETE /api/v1/connections/填上Token合約地址
```

#### 轉帳

```
POST /api/1/transfers/填上Token合約地址/填上另一個節點地址
{
    "amount": 42
}
```

#### 關閉Channel

```
PATCH /api/1/channels/填上Channel地址
{
    "state":"closed"
}
```

#### 呼叫Settle

在關閉Channel後，經過一個timeout時間，可以呼叫以下指令，將雙方最後的餘額發回雙方地址中。

```
PATCH /api/1/channels/填入channel地址
{
    "state":"settled"
}
```

#### 

#### 安裝時可能產生的Issue：

[https://github.com/raiden-network/raiden/issues/1235](https://github.com/raiden-network/raiden/issues/1235)

[https://github.com/ethereum/pyethapp/issues/209\#issuecomment-308466232](https://github.com/ethereum/pyethapp/issues/209#issuecomment-308466232)

