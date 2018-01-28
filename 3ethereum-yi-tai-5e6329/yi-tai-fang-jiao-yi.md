# 以太坊交易

在以太坊中的交易通常有以下幾種類型，一種是傳送給另一個持有私鑰的地址，另一種是執行合約的交易以及傳送以太幣給合約地址。

我們可以用如下的Web3程式來簡單發出一筆交易。

```js
web3.eth.sendTransaction({
  from: 傳送人地址,
  to: 接收者地址,
  value: 金額（Wei）,
  gas: 使用的Gas Limit,
  gasPrice: 使用的Gas Price,
  data: 額外附加的Data,
  nonce: 目前交易的nonce，需要是此帳號的上一筆交易nonce加上1，或是使用相同nonce來覆蓋上一筆pending中的交易
}, function(err, result) {
  if(err) console.log(err);
  console.log(result);
})
```

#### 對交易簽名

在Metamask中如果用到 `sendTransaction` 或是 `sign` 時會自動跳出確認視窗，點擊確認後即可發送出交易。

而如果是在Geth console中`unlock Account`後再發出交易則會自動簽名，另外我們也可以使用以下程式自行用私鑰對交易進行簽名。

需要先安裝

```
npm install ethereumjs-tx
```

```js
const Tx = require('ethereumjs-tx');

// 將以下換為其他私鑰，以下僅為範例私鑰
let privateKey_hex = 'e331b6d69882b4cb4ea581d88e0b604039a3de5967688d3dcffdd2270c0fd109';
const privateKey = new Buffer(privateKey_hex, 'hex')

var rawTx = {
  nonce: '0x00', // 設定nonce
  gasPrice: '0x09184e72a000',  // 設定gasPrice
  gasLimit: '0x2710', // 設定gasLimit
  to: '0x0000000000000000000000000000000000000000',  // 設定收款人
  value: '0x00',  // 金額
  data: '0x4920676f74206f6e652062616e616e61' // 額外附加的Data，或是執行合約Function之Data
}

let tx = new Tx(rawTx);
tx.sign(privateKey); // 簽名

let serializedTx = tx.serialize();

web3.eth.sendRawTransaction('0x' + serializedTx.toString('hex'), function(err, hash) {
  if (!err)
    console.log(hash); // 回傳交易之Tx hash
});
```

#### 在交易中輸入自訂訊息

把要輸入的Data先轉為Hex

```js
function toHex(str) {
  let hex = '';
  for(let i = 0; i < str.length; i++ ) {
    hex += ''+str.charCodeAt(i).toString(16);
  }
  return hex;
}

console.log(toHex('I got one banana'));
// 4920676f74206f6e652062616e616e61
```

> 或是可用線上工具：[http://string-functions.com/string-hex.aspx](http://string-functions.com/string-hex.aspx)

然後我們使用Metamask進行轉帳 \( 也可自行選擇其他錢包轉帳 \)

在下方Gas Limit的部分一開始是預設21000，但因為我們帶有Input Data所以要給他更多的Gas

**計算方式如下 :**

1.我們先到以太坊黃皮書 [https://ethereum.github.io/yellowpaper/paper.pdf](https://ethereum.github.io/yellowpaper/paper.pdf)

2.找到附錄 Appendix G. Fee Schedule 的部分

![](/assets/螢幕快照 2018-01-28 上午9.46.49.png)

```
 可以看到上圖中說明，每加上一個 non-zero的byte需要多給 68Gas。
```

3..我們加上的Data included可以看到Metamask下方顯示總共為16 bytes \( 32個hex字 \) ，所以如下計算

```
(16 * 68) + 21000
```

> 以上計算得到22088，我們把zero byte一樣以 68 Gas 計算。
>
> 如果Gas 給得不夠會顯示[intrinsic gas too low](https://ethereum.stackexchange.com/questions/1570/mist-what-does-intrinsic-gas-too-low-mean)

4.之後更改發出交易時的Gas Limit

![](/assets/螢幕快照 2018-01-28 上午9.29.47.png)

成功交易後到Etherscan.io查看帶有Input Data的交易，即可看到下方Input Data出現剛才輸入的訊息。

![](/assets/螢幕快照 2018-01-28 上午9.40.14.png)

[https://ropsten.etherscan.io/tx/0x469f79a1b24a6d306c20c3111562d01e36146d6524c2f33a11c5db45d9325c71](https://ropsten.etherscan.io/tx/0x469f79a1b24a6d306c20c3111562d01e36146d6524c2f33a11c5db45d9325c71)

