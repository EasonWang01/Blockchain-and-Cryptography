# Payload

此為訊息的主體部分，大部分的節點訊息交換資料都會在這。

> 可參考比特幣開發者官網
>
> [https://bitcoin.org/en/developer-reference\#message-headers](https://bitcoin.org/en/developer-reference#data-messages)

其中 Payload 訊息分為兩種類型：Data Messages 與 Control Messages

# 1.Data Messages

包含了所有與交易跟區塊有關的請求：

![](/assets/螢幕快照 2018-02-01 下午9.33.33.png)

# 2.Control Messages

包含了節點之間連線相關的請求：

![](/assets/螢幕快照 2018-02-01 下午9.33.51.png)

# 節點訊息傳送內容

以下以Version訊息為範例 :

> 在官方資料上節點分為 『 transmitting node \(發出方\) 』 與 『 receiving node（接收方）』，如果請求從本地發出則本地的節點即為transmitting node\(發出方\)角色，反之。

* Protocol version: 70005 \( 7f110100 \)

* > 4 bytes，表達當前使用之 Bitcoin protocol版本，可參考： [https://bitcoin.org/en/developer-reference\#protocol-versions](https://bitcoin.org/en/developer-reference#protocol-versions)
* Services: NODE\_NETWORK （ 0100000000000000 ）

* > 8 bytes ，0x01 為 Full node
* Epoch Time: 1513492007

* > 8 bytes， 此為unix的timestamp 但只取到秒數  `parseInt(Date.now().toString().substring(0, 10)).toString('16')` 之後在後面補上0
* Receiving node's services: 0100000000000000

  > 8 bytes， 通常值會和上面Services: NODE\_NETWORK相同

* Receiving node's IPv6 address：00000000000000000000ffffc61b6409

  > 16 bytes， 為接收方節點IP地址 類似如下 ::ffff:127.0.0.1

```
7f110100 ........................... Protocol version: 70005
// 4 bytes
// 目前Bitcoin protocol版本 https://bitcoin.org/en/developer-reference#protocol-versions

0100000000000000 ................... Services: NODE_NETWORK
// 8 bytes
// 0x01為full node

5a360e2700000000 ................... Epoch time: 1513492007   
( 8 bytes 此為unix的timestamp 但只取到秒數 )
( parseInt(Date.now().toString().substring(0, 10)).toString('16') 之後在後面補上0 )

0100000000000000 ................... Receiving node's services
(8 bytes 通常值會和上面Services: NODE_NETWORK相同)



Receiving node's IPv6 address：00000000000000000000ffffc61b6409
( 16 bytes 為接收方節點IP地址   類似如下 ::ffff:127.0.0.1 )

Receiving node's port number : 208d
( 2 bytes 為接收方節點PORT)

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

以下為完整的傳送Version訊息的範例程式

```js
const dns = require('dns');
const net = require('net');
const crypto = require('crypto');

const payload = {
  Protocol_version: "7f110100", //70015
  Node_services: "0d00000000000000", // 0x000000000000000d
  Timestamp: BigEndian_to_SmallEndian(parseInt(Date.now().toString().substring(0, 10)).toString('16')) + "00000000",

  // Address as receiving node
  Node_services_rec: "0d00000000000000",// 0x000000000000000d
  Node_address_rec: "00000000000000000000ffff2e043c24", //::ffff:46.4.60.36
  Node_port_rec: "208d", //8333 

  // Address as emitting node
  Node_services_emit: "0d00000000000000",// 0x000000000000000d
  Node_address_emit: "00000000000000000000000000000000",
  Node_port_emit: "0000",

  Random_nonce: "75ba7abb00a0f633", //0x33f6a000bb7aba75

  //User agent
  User_agent_Count: "10", // 16
  User_agent_String: "2f5361746f7368693a302e31332e322f", ///Satoshi:0.13.2/

  Block_start_height: "a0040000",// 1184
  Relay_flag: "01" // 1
}

// 把payload 字串接起來
let payload_string = "";
for(let i = 0; i < Object.keys(payload).length ; i++){
  payload_string += payload[Object.keys(payload)[i]]
}

/*--- Payload Header --- */
const magicNum = "f9beb4d9";
const control_version = "76657273696f6e0000000000"// 英文version字串 轉為 ascii hex;
const payloadLength = (payload_string.length / 2).toString('16') + "000000" // 以bytes計算後轉為16進位，後面再補0
const payload_checksum = double_sha256(payload_string); // 計算payload 的 checksum
/* ----------------------- */


// 把 message header 拼接 message payload，此為我們要發送出去的封包內容
const message_header = magicNum + control_version + payloadLength + payload_checksum;
const message = message_header + payload_string;

var host;
var hostList;
var try_host_No = 0;
var timeout_ = 2000;
const options = {
  family: 4,
  hints: dns.ADDRCONFIG | dns.V4MAPPED,
};
options.all = true;
dns.lookup('seed.bitcoin.sipa.be', options, (err, addresses) => { //先找到可用節點
  hostList = addresses; //返回的IP中的第一個

  const buffer1 = new Buffer(message, 'hex');
  connectPeer(hostList[try_host_No].address, buffer1)

});

function connectPeer(host, buffer1) {
  const client = net.createConnection({ port: 8333, host }, () => {
    //'connect' listener
    console.log(`connected to other peers,host ${try_host_No}`);
    client.write(buffer1);
  });
  client.on('data', (data) => {
    console.log(data.toString());
    client.end();
  });
  client.on('end', () => {
    console.log('disconnected from other peers');
  });
  client.on('error', (err) => {
    console.log(err);
    client.end();
  });

  //預設timeout 為兩秒
  client.setTimeout(timeout_);
  client.on('timeout', () => {
    console.log(`socket timeout for host ${try_host_No}`);
    client.end();
    // 如果連線失敗繼續嘗試下個節點
    try_host_No += 1;
    connectPeer(hostList[try_host_No].address, buffer1);
  });
}



function BigEndian_to_SmallEndian(hexNum) {
  let SmallEndian_array = [];
  if (hexNum.length % 2 !== 0) {
    hexNum = '0' + hexNum
  }
  for (let i = 0, len = hexNum.length; i < len; i++) {
    if (i % 2 !== 0) {
      SmallEndian_array.unshift(hexNum.charAt(i - 1) + hexNum.charAt(i))
    }
  }
  return SmallEndian_array.join('');
}

function double_sha256(payload) {
  if(payload === "") return Buffer.from("5df6e0e2", "hex");
  payload = Buffer.from(payload, "hex")
  let sha256 = crypto.createHash('sha256').update(payload).digest();
  return crypto.createHash('sha256').update(sha256).digest().slice(0, 4).toString('hex');
}
```

---

> 我們看到的Hex 都屬於Little endian 也稱為 Small endian \( 附註1 \)
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

比特幣封包內容均以 16進位的 Small Endian表示

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

附註1:

Big-Endian與Small-Endian主要用來描述記憶體位置的擺放順序，名詞出現於 Swift 創作的著名小說

"_Gulliver's Travels“，看到以下範例_

```
假設有一個Data = 0x12345678

在Big-Endian為： 0x12 0x34 0x56 0x78
在Small-Endian為： 0x78 0x56 0x34 0x12
```



