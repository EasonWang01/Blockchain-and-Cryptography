## Web3.js

[https://github.com/ethereum/web3.js/](https://github.com/ethereum/web3.js/)

在客戶端程式中，例如網頁，為了讀取區塊鏈的資料必須使用一些方法來存取，而在web3.js及為一個可以方便網頁端存取Ethereum區塊鏈資料的第三方函式庫，為Javascript語言寫成。

> 另一個類似的專案為: [https://github.com/ethjs/ethjs](https://github.com/ethjs/ethjs)

# 安裝

```
npm install web3

或是

yarn add web3
```

## 使用

初始化，設定要讀取哪一個節點上的資料。

```js
if (typeof web3 !== 'undefined') {
  web3 = new Web3(web3.currentProvider); // 如果有安裝Metamask則會是Metamask
} else {
  // 假設沒有預設的provider
  const Web3 = require('web3');
  web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
}
```

> 如果在程式是寫localhost為provider，但是在有安裝Metamask的瀏覽器打開此Dapp，則Chrome devtool的console輸入web3時會去讀取Metamask之RPC，但寫在程式中的web3.eth.accounts還是會去讀取localhost RPC

為了快速的理解使用方式，我們先輸入以下指令

> 我們使用的前端框架為React.js，而create-react-app這個模組可以快速地幫我們架構好一個網頁程式

```
npm install -g create-react-app
create-react-app my-dapp
cd my-dapp
```

安裝相關模組並啟動

```
npm install web3@0.20.4 
npm start
```

## 

## 常用指令

初始化合約

```
const MyContract = web3.eth.contract(合約ABI).at(合約地址);
```

顯示金額為Ether

```
web3.fromWei(web3.eth.getBalance(<帳號>))
```



