# Bitcoin 測試鏈\(Testnet\)

比特幣在加入新功能之前，必須要先進行內部測試，所以會先將功能放到測試鏈上進行測試，但測試鏈的比特幣不具任何實際價值。  
[https://en.bitcoin.it/wiki/Testnet](https://en.bitcoin.it/wiki/Testnet)

上一章節我們所開啟的Bitcoin-QT錢包也可以與Testnet做連線，輸入以下指令：

```
<...Path>/bitcoin-qt.exe -testnet -datadir="可指定要存放鏈資料的資料夾"
```

> Windows 在前面加上start  
> ![](/assets/testnet-qt.png)

看到Bitcoin icon顯示為綠色，代表目前連線到Testnet。

### 產生Testnet的地址

點選視窗上方之說明Tab，然後點選`除錯視窗`，開啟後於下方輸入`getnewaddress`  
![](/assets/testnet-address.png)  
可看到產生`m`開頭的測試鏈地址。

#### 線上產生比特幣地址

我們也可以用線上網站服務產生測試鏈的比特幣地址，到以下網址：

[https://www.bitaddress.org/?testnet=true](https://www.bitaddress.org/?testnet=true)

> 記得後面要加上參數 : /?testnet=true  
> ![](/assets/testonlineaddress.png)  
> 進去之後移動滑鼠，等到100%後即會產生地址，之後可以按Generate New Address 產生新地址，產生地址後務必記錄所產生之Address與Private key。

#### 離線產生比特幣地址

離線產生地址可以減少私鑰被第三者監聽或是竊取的風險。

可使用如下程式產生測試鏈地址，以下會產生一個長度為 32 bytes 的隨機值，然後用該隨機值產生WIF格式的私鑰以及地址。

> 需要使用Node.js並且安裝bitcoinjs-lib模組。
>
> ```
> npm install bitcoinjs-lib
> ```

```js
const crypto = require('crypto');
const bitcoin = require('bitcoinjs-lib');

const rng = () => crypto.randomBytes(32);
const testnet = bitcoin.networks.testnet;
const keyPair = bitcoin.ECPair.makeRandom({ network: testnet, rng });
const wif = keyPair.toWIF();
const address = keyPair.getAddress();

console.log('--私鑰--');
console.log(wif);
console.log('--地址--');
console.log(address);
```

### 取得測試用的比特幣

我們可以到此網站取得免費測試用的比特幣：

[https://testnet.manu.backend.hamburg/faucet](https://testnet.manu.backend.hamburg/faucet)

> ![](/assets/getfreecoin.png)在上方框框填上地址，並且點選reCAPTCHA。

成功後會出現交易的TxID，如下圖：

![](/assets/getfreecoin1.png)

之後我們可以點擊連結，查看所產生的交易資訊：

![](/assets/getfreecoin2.png)

然後到blockexplorer這個網站確認我們地址的餘額：

[https://testnet.blockexplorer.com/](https://testnet.blockexplorer.com/)

> 在本地端的節點必須要完整同步後才可以得到正確的區塊鏈資訊，所以建議讀者可使用線上的網站服務，該網站會去存取已經完整同步過的Full Node，並回傳資訊到網站上供瀏覽。

### ![](/assets/getfreecoin3.png)

### 廣播交易

當我們將交易簽名後，需要將其廣播到其他節點做驗證，才算正式的發送出交易，有關交易廣播細節將會在後續交易章節詳細敘述。

我們可以使用以下網站之服務，廣播測試鏈的交易：

[https://live.blockcypher.com/btc-testnet/pushtx/](https://live.blockcypher.com/btc-testnet/pushtx/)

### ![](/assets/螢幕快照 2018-01-21 上午10.12.20.png)

或是可用以下程式廣播交易，記得在tx內填入要廣播的交易Hex碼。

```js
// 廣播交易
const https = require('https');

var postData = JSON.stringify({
  tx: "填入要廣播的交易Hex碼"
});

var options = {
  hostname: 'api.blockcypher.com',
  port: 443,
  path: '/v1/btc/test3/txs/push',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': postData.length
  }
};

var req = https.request(options, (res) => {
  console.log('statusCode:', res.statusCode);
  console.log('headers:', res.headers);

  res.on('data', (d) => {
    process.stdout.write(d);
  });
});

req.on('error', (e) => {
  console.error(e);
});

req.write(postData);
req.end();
```

### 查詢交易

在以下網站的最後面輸入交易的TxID，來查詢該筆交易：

> [https://live.blockcypher.com/btc-testnet/tx/填入Txid](https://live.blockcypher.com/btc-testnet/tx/填入Txid)

也可以使用其 API 進行查詢 \( 會返回JSON格式資料 \)：[https://api.blockcypher.com/v1/btc/test3/txs/填入Txid](https://api.blockcypher.com/v1/btc/test3/txs/填入Txid)

> 相關 Blockcypher 服務的 API 文件可到此查看：[https://www.blockcypher.com/dev/bitcoin/\#restful-resources](https://www.blockcypher.com/dev/bitcoin/#restful-resources)



