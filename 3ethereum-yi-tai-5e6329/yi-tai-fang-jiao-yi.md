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

