## Web3

[https://github.com/ethereum/web3.js/](https://github.com/ethereum/web3.js/)

為Javascript語言之Ethereum API

# 安裝

```
npm install web3

或是

yarn add web3
```

## 使用

初始化

```js
if (typeof web3 !== 'undefined') {
  web3 = new Web3(web3.currentProvider);
} else {
  // 假設沒有預設的provider
  const Web3 = require('web3');
  web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
}
```

## 常用指令

顯示金額為Ether

```
web3.fromWei(web3.eth.getBalance(<帳號>))
```



