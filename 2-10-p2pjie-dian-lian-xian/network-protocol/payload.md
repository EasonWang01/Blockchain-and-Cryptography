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

> 比特幣封包內容均以 16進位的 Small Endian表示\(注1\)
>
> BigEndian 轉 SmallEndian可用此程式轉換：
>
> ```js
> function BigEndian_to_SmallEndian(hexNum) {
>   let SmallEndian_array = [];
>   if (hexNum.length % 2 !== 0) {
>     hexNum = '0' + hexNum
>   }
>   for (let i = 0, len = hexNum.length; i < len; i++) {
>     if (i % 2 !== 0) {
>       SmallEndian_array.unshift(hexNum.charAt(i - 1) + hexNum.charAt(i))
>     }
>   }
>   return SmallEndian_array.join('');
> }
> ```

# 節點訊息傳送

1. version

> 在官方資料上節點分為 『 transmitting node \(發出方\)』 與 『 receiving node（接收方） 』，如果請求從本地發出則本地的節點及擔任transmitting node\(發出方\)角色，反之。

```
7f110100 ........................... Protocol version: 70005
// 4 bytes
// 目前Bitcoin protocol版本 https://bitcoin.org/en/developer-reference#protocol-versions

0100000000000000 ................... Services: NODE_NETWORK
// 8 bytes
// 0x01為full node

5a360e2700000000 ................... Epoch time: 1513492007   
// 8 bytes
// 此為unix的timestamp 但只取到秒數 
// parseInt(Date.now().toString().substring(0, 10)).toString('16') 之後在後面補上0

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
// 8 bytes
// 為一個隨機值

0f ................................. Bytes in user agent string: 15
// 為下面user-agent字串長度的bytes數

2f5361746f7368693a302e392e332f ..... User agent: /Satoshi:0.9.3/
// 為user-agent版本的值
// EX: /Satoshi:0.13.2/

cf050500 ........................... Start height: 329167
// 傳輸方節點目前資料的最高區塊高度

01 ................................. Relay flag: true
// 1 bytes
// 如果為0x01代表節點需要 inv messages 與 tx messages
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

