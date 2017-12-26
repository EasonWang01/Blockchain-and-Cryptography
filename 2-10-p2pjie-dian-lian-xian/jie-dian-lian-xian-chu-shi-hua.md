# 找到連線節點後會進行如下步驟:

# 1. version與 verack

```
Step 1: A 節點送出 verison 請求
Step 2: B 節點送出 version 與 verack 請求 
Step 3: A 節點送出 verack 請求
```

![](/assets/螢幕快照 2017-12-18 下午11.08.24.png)

節點會先送出一個[`version`message](https://bitcoin.org/en/developer-reference#version) 給目標連線的節點，而該請求被另一個節點接收到並接收後會回覆[`verack`message](https://bitcoin.org/en/developer-reference#verack)，這跟TCP的握手階段類似。

> version message發送，寫於原始碼，如下圖![](/assets/ˊ啊6876.png)[https://github.com/bitcoin/bitcoin/blob/d3cb2b8acfce36d359262b4afd7e7235eff106b0/src/net.cpp\#L562](https://github.com/bitcoin/bitcoin/blob/d3cb2b8acfce36d359262b4afd7e7235eff106b0/src/net.cpp#L562)

![](/assets/螢幕快照 2017-12-18 下午11.24.49.png)

可用以下程式模擬，發送出version請求

```js
const buffer = new Buffer('f9beb4d976657273696f6e000000000066000000c0a049f67f1101000d000000000000003ddc275a000000000d0000000000000000000000000000000000ffff2e043c24208d0d00000000000000000000000000000000000000000000000000659885d88df91a01102f5361746f7368693a302e31332e322f6000000001','hex');

const net = require('net');
const client = net.createConnection({ port: 8333, host: "46.4.60.36" }, () => {
  // 成功連線
  console.log('connected to server!');
  client.write(buffer);
});
client.on('data', (data) => {
  // 接收到訊息
  console.log(data.toString());
  client.end();
});
client.on('end', () => {
  // 連線結束
  console.log('disconnected from server');
});
```

但真實情況下我們需要先從DNS server中找到可用的連線節點，並且測試連線，如果連線失敗則繼續嘗試下一個節點，所以可以將程式修改為以下

```js
const dns = require('dns');
const net = require('net');

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
  hostList = addresses; //DNS server返回的IP列表

  const buffer = new Buffer('f9beb4d976657273696f6e000000000066000000e253144d7f1101000d000000000000005a01365a000000000d0000000000000000000000000000000000ffff2e043c24208d0d0000000000000000000000000000000000000000000000000075ba7abb00a0f633102f5361746f7368693a302e31332e322fa004000001', 'hex');
  connectPeer(hostList[try_host_No].address, buffer)

});

function connectPeer(host, buffer) {
  const client = net.createConnection({ port: 8333, host }, () => {
    // 成功連線
    console.log(`connected to other peers,host ${try_host_No}`);
    client.write(buffer);
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
    try_host_No += 1;
    connectPeer(hostList[try_host_No].address, buffer);
    client.end();
  });

  //預設timeout 為兩秒
  client.setTimeout(timeout_);
  client.on('timeout', () => {
    console.log(`socket timeout for host ${try_host_No}`);
    client.end();
    // 如果連線失敗繼續嘗試下個節點
    try_host_No += 1;
    connectPeer(hostList[try_host_No].address, buffer);
  });
}
```

> 封包詳細內容解析將於後面章節詳細描述

# 2. getaddr與addr

getaddr用來發送請求給其他節點，addr為該節點返回其所擁有的鄰居地址

#### getaddr: \( 不具有payload \)

![](/assets/螢幕快照 2017-12-18 下午11.20.28.png)

將上面程式的 Buffer 部分改為如下再次執行，即可模擬getaddr。

```js
const buffer = new Buffer('f9beb4d9676574616464720000000000000000005df6e0e2', 'hex');
```

#### addr:

![](/assets/螢幕快照 2017-12-18 下午11.20.49.png)

```js
const buffer = new Buffer('f9beb4d96164647200000000000000001f0000005b2b59ce0154bf415a8d0000000000000000000000000000000000ffff3438b5fb208d', 'hex');
```

[https://bitcoin.org/en/developer-reference\#addr](https://bitcoin.org/en/developer-reference#addr)

## 3. Ping與Pong

Ping 用來確認另一個節點是否仍處於連線狀態，對方節點會回覆一個pong訊息，告知目前仍在連線。

Pong 回覆中的nonce欄位會和接收到的 ping 請求之nonce相同。

> [protocol version 60000](https://bitcoin.org/en/developer-reference#protocol-versions) 之前 ，ping 請求不帶有payload ，[protocol version 60001](https://bitcoin.org/en/developer-reference#protocol-versions) 後，ping請求帶有一個 nonce有欄位

#### Ping

![](/assets/螢幕快照 2017-12-26 上午11.26.34.png)

```js
const buffer = new Buffer('f9beb4d970696e67000000000000000008000000ca8bfa584d3181fbb3121e1b', 'hex');
```

#### Pong

![](/assets/螢幕快照 2017-12-26 上午11.30.33.png)

```js
const buffer = new Buffer('f9beb4d9706f6e670000000000000000080000002ddab80398a059a304ce08a9', 'hex');
```



## Mempool

節點通常在啟動後會隨即發出mempool請求，要求取得目前還沒被確認的交易到自己節點的交易池中

來等待驗證，節點發出mempool之後收到的回覆訊息為Inv。

![](/assets/螢幕快照 2017-12-26 下午7.38.28.png)

mempool的請求不帶有payload

![](/assets/螢幕快照 2017-12-26 下午7.42.45.png)

範例:

```js
  const magicNum = "f9beb4d9";
  const command = "6d656d706f6f6c0000000000" // mempool
  const payload_length = "00000000"
  const checksum = "5df6e0e2"

  const buffer = new Buffer(magicNum + command + payload_length + checksum, 'hex');
```



