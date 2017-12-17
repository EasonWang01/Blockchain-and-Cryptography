The[`version`message](https://bitcoin.org/en/developer-reference#version)provides information about the transmitting[node](https://bitcoin.org/en/glossary/node)to the receiving[node](https://bitcoin.org/en/glossary/node)at the beginning of a connection. Until both[peers](https://bitcoin.org/en/glossary/node)have exchanged[`version`messages](https://bitcoin.org/en/developer-reference#version), no other messages will be accepted.

If a[`version`message](https://bitcoin.org/en/developer-reference#version)is accepted, the receiving[node](https://bitcoin.org/en/glossary/node)should send a[`verack`message](https://bitcoin.org/en/developer-reference#verack)—but no[node](https://bitcoin.org/en/glossary/node)should send a[`verack`message](https://bitcoin.org/en/developer-reference#verack)before initializing its half of the connection by first sending a[`version`message](https://bitcoin.org/en/developer-reference#version).

### 1.節點會先送出一個[`version`message](https://bitcoin.org/en/developer-reference#version) 給目標連線的節點，而該請求被另一個節點接收到並接收後會回覆[`verack`message](https://bitcoin.org/en/developer-reference#verack)

> version message發送，寫於原始碼，如下圖![](/assets/ˊ啊6876.png)[https://github.com/bitcoin/bitcoin/blob/d3cb2b8acfce36d359262b4afd7e7235eff106b0/src/net.cpp\#L562](https://github.com/bitcoin/bitcoin/blob/d3cb2b8acfce36d359262b4afd7e7235eff106b0/src/net.cpp#L562)

可用以下程式模擬，發送出version請求

```js
const buffer1 = new Buffer('f9beb4d976657273696f6e000000000066000000c0a049f67f1101000d000000000000003ddc275a000000000d0000000000000000000000000000000000ffff2e043c24208d0d00000000000000000000000000000000000000000000000000659885d88df91a01102f5361746f7368693a302e31332e322f6000000001','hex');

const net = require('net');
const client = net.createConnection({ port: 8333, host: "46.4.60.36" }, () => {
  //'connect' listener
  console.log('connected to server!');
  client.write(Buffer.concat([buffer1]));
});
client.on('data', (data) => {
  console.log(data.toString());
  client.end();
});
client.on('end', () => {
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

  const buffer1 = new Buffer('f9beb4d976657273696f6e000000000066000000e253144d7f1101000d000000000000005a01365a000000000d0000000000000000000000000000000000ffff2e043c24208d0d0000000000000000000000000000000000000000000000000075ba7abb00a0f633102f5361746f7368693a302e31332e322fa004000001', 'hex');
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
```



