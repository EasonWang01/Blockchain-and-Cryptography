# Payload

此為訊息的主體部分

> 可參考比特幣開發者官網
>
> [https://bitcoin.org/en/developer-reference\#message-headers](https://bitcoin.org/en/developer-reference#data-messages)

訊息分為兩種類型：Data Messages 與 Control Messages

# 1.Data Messages

包含了所有與交易跟區塊有關的網路請求。

![](/assets/螢幕快照 2017-12-10 下午9.00.10.png)

# 2.Control Messages

![](/assets/螢幕快照 2017-12-10 下午9.00.17.png)

# 節點訊息傳送

1. version

```
7f110100 ........................... Protocol version: 70005
// 4 bytes
// 目前Bitcoin protocol版本 https://bitcoin.org/en/developer-reference#protocol-versions

0100000000000000 ................... Services: NODE_NETWORK
// 8 bytes
// 0x01為full node

5a360e2700000000 ................... Epoch time: 1513492007   
// 9 bytes
// 此為unix的timestamp 但只取到秒數 
// parseInt(Date.now().toString().substring(0, 10)).toString('16')

0100000000000000 ................... Receiving node's services
// 8 bytes
//通常值會和上面Services: NODE_NETWORK相同

00000000000000000000ffffc61b6409 ... Receiving node's IPv6 address
// 16 bytes 
// 為接收方節點IP地址   類似如下 ::ffff:127.0.0.1

208d ............................... Receiving node's port number
// 2 bytes 
// 為接收方節點PORT

0100000000000000 ................... Transmitting node's services
// 8 bytes
// 和上面Services: NODE_NETWORK相同


00000000000000000000ffffcb0071c0 ... Transmitting node's IPv6 address
// 16 bytes 
// 為傳輸方節點IP地址   類似如下 ::ffff:127.0.0.1

208d ............................... Transmitting node's port number
// 為傳輸方節點PORT

128035cbc97953f8 ................... Nonce

0f ................................. Bytes in user agent string: 15
2f5361746f7368693a302e392e332f ..... User agent: /Satoshi:0.9.3/

cf050500 ........................... Start height: 329167
01 ................................. Relay flag: true
```

.

.

.

---

> 我們看到的Hex 都屬於Little endian
>
> ```
> 如果有一個數字：70015 轉為16進位後為 1117F
>
> 這時因為兩個數字為一個bytes 可將其分為三個bytes: 01 11 7f
>
> 將這三個bytes前後順序(byte order)轉換
>
> 可得到 7f 11 01
> ```

![](/assets/螢幕快照 2017-12-15 下午4.16.07.png)

