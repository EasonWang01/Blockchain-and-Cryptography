# 以太坊交易

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

![](/assets/螢幕快照 2018-01-28 上午9.29.47.png)

成功交易後到Etherscan.io查看帶有Input Data的交易，即可看到下方Input Data出現剛才輸入的訊息。

![](/assets/螢幕快照 2018-01-28 上午9.40.14.png)

[https://ropsten.etherscan.io/tx/0x469f79a1b24a6d306c20c3111562d01e36146d6524c2f33a11c5db45d9325c71](https://ropsten.etherscan.io/tx/0x469f79a1b24a6d306c20c3111562d01e36146d6524c2f33a11c5db45d9325c71)

