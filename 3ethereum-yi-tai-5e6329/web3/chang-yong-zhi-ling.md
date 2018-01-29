# 常用指令

#### 設定要讀取哪一個節點上的資料。

```js
if (typeof web3 !== 'undefined') {
  web3 = new Web3(web3.currentProvider); // 如果有安裝Metamask則會是Metamask
} else {
  // 假設沒有預設的provider
  const Web3 = require('web3');
  web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
}
```

> 如果在程式是寫localhost為provider，但是在有安裝Metamask的瀏覽器打開此Dapp，則Chrome devtool的console輸入web3時會去讀取Metamask之RPC，但寫在程式中的web3.eth.accounts還是會去讀取localhost RPC。

也可以使用第三方提供的節點來取得資訊，例如[https://infura.io/](https://infura.io/)

![](/assets/螢幕快照 2018-01-28 下午4.01.29.png)

#### 指定合約目標

```js
const Contract = web3.eth.contract(合約ABI).at(合約地址);
```

#### 轉換單位

從Wei轉為其他單位 :

```js
web3.fromWei(1000000000000000000, "ether")
```

從其他單位轉為Wei :

```
web3.toWei('1', 'ether');
```

可用單位包含 :

```
kwei/ada
mwei/babbage
gwei/shannon
szabo
finney
ether
kether/grand/einstein
mether
gether
tether
```

#### 查看帳號餘額

```
eth.getBalance(帳號)
```

使用Ether表示：

```
web3.fromWei(eth.getBalance(帳號), "ether")
```

#### 估計可能使用多少單位個Gas

```js
web3.eth.estimateGas({
    to: <合約>.address, 
    data: <合約>.<合約Function>.getData(<合約Function參數>)
});
```

> 也可使用Remix IDE的 Detail 按鈕點擊後下拉到 GASESTIMATES 部分查看，但可能會比較不準確。

[https://github.com/ethereum/wiki/wiki/JavaScript-API\#web3ethestimategas](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethestimategas)

#### 執行合約Function

```js
Contract.合約Function(參數, {from: web3.eth.accounts[0], gas: 111700})
```

> 如果合約Function有參數，則記得在最後面加上 `{from: 執行交易人地址, gas: gas使用量}`即可
>
> gas 參數指的是 gas Limit

#### 發送交易

```
eth.sendTransaction({from:eth.coinbase, to:eth.accounts[1], value: web3.toWei(0.05, "ether")})
```

> 可以設定gas Limit 與 gas Price ： `{gas: ..., gasPrice: ...}`

範例：

```js
web3.eth.sendTransaction({
  from:web3.eth.coinbase, to:web3.eth.accounts[1], value: web3.toWei(0.0005, "ether"), 
  gas: 201100, gasPrice: 18000000000}, 
  function(err, result) {
    if(err) console.log(err);  
    console.log(result);
})
```

> 與交易相關的還有nonce值，每次發送交易後該nonce值都會加一，如果該地址後面發出的交易nonce小於上一筆發出交易的nonce則無法成功發送。

#### 取得目前鏈上的Gas Price

```
web3.eth.getGasPrice(function(err, result) {console.log(result)})
```

#### 檢查連線

```
web3.isConnected()
```

範例:

```js
if(!web3.isConnected()) {
   // 尚未連線到節點
} else {
   // 與節點連線中
}
```

#### 讀取目前預設帳號

```
web3.eth.defaultAccount
```

當我們使用Metamask時選擇不同帳號，`defaultAccount` 也會跟著更改。

會影響以下兩個Function執行時選擇的帳號。

```js
web3.eth.sendTransaction()
web3.eth.call()
```

> 但也可以在Function 設定 `{ from: 帳號 }` 來修改

如果要修改default account直接在後面加上 `= <帳號>`  即可

```
web3.eth.defaultAccount = "0x76d87e6a4069e4a463ec6a1eb7b73738282906d5"
```

改變defaultAccount後 coinbase 帳號也會跟著變，coinbase帳號是指挖礦後的獎勵會分配給哪個帳號。

```
web3.eth.coinbase
```

> `web3.eth.coinbase`方法只可讀取，無法在後面加上`=` 進行修改

#### 取得特定區塊之資料

```js
web3.eth.getBlock(區塊號碼, function(error, result){
    if(!error)
        console.log(result)
    else
        console.error(error);
})
```

或是使用

```
web3.eth.getBlock(區塊號碼)
```

取得區塊內包含的交易數目

```
web3.eth.getBlockTransactionCount(區塊號碼或Hash)
```

#### 取得交易的內容

```
web3.eth.getTransaction(交易Hash)
```

或是

```
web3.eth.getTransactionReceipt(交易Hash)
```

> pending 中的 transactions 無法使用 getTransactionReceipt

#### 取得特定地址發送過多少交易

```
web3.eth.getTransactionCount(地址)
```

#### 查看節點是否正在進行同步

```
web3.eth.getSyncing(function(error, result){ console.log(result) })
```

或是

```
web3.eth.syncing
```

也可以使用監聽的方式，在每次有新區塊時都會通知

```
web3.eth.isSyncing(callback);
```

#### 查看目前連線到哪個網路

```js
web3.version.getNetwork((err, netId) => {
  switch (netId) {
    case "1":
      console.log('This is mainnet')
      break
    case "2":
      console.log('This is the deprecated Morden test network.')
      break
    case "3":
      console.log('This is the ropsten test network.')
      break
    case "4":
      console.log('This is the Rinkeby test network.')
      break
    case "42":
      console.log('This is the Kovan test network.')
      break
    default:
      console.log('This is an unknown network.')
  }
})
```

> [https://github.com/MetaMask/faq/blob/master/DEVELOPERS.md\#construction\_worker-network-check](https://github.com/MetaMask/faq/blob/master/DEVELOPERS.md#construction_worker-network-check)



