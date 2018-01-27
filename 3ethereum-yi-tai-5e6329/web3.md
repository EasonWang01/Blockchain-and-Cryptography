## Web3.js

官方Github : [https://github.com/ethereum/web3.js/](https://github.com/ethereum/web3.js/)

官方文件 : [https://web3js.readthedocs.io/en/1.0/web3.html](https://web3js.readthedocs.io/en/1.0/web3.html)

在客戶端程式中，例如網頁，為了讀取區塊鏈的資料必須使用一些方法來存取，而在web3.js及為一個可以方便網頁端存取Ethereum區塊鏈資料的第三方函式庫，為Javascript語言寫成。

> 另一個類似的專案為: [https://github.com/ethjs/ethjs](https://github.com/ethjs/ethjs)

# 安裝

```
npm install web3

或是

yarn add web3
```

> 本書使用版本為`0.20.3`

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

接著我們把App.js改為如下

```js
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import Web3 from 'web3';
const web3 = new Web3();

class App extends Component {

  constructor() {
    super();
    this.state = {
      accounts: '',

    }
  }

  componentWillMount() {
    web3.setProvider(new web3.providers.HttpProvider('http://localhost:8545')); //指定為RPC server的位置
    this.setState({ accounts: web3.eth.accounts });
  }

  render() {
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to My Dapp</h2>
        </div>
        <div className="App-intro">
          {this.state.accounts.map((i, idx) => (
            <p key={idx}>帳號{idx}: {i}，餘額: {web3.fromWei(web3.eth.getBalance(i)).toString()} </p>
          ))}
        </div>
      </div>
    );
  }
}

export default App;
```

即可看到網頁上讀取出帳號。

接著我們回到Geth console，輸入如下新增帳號

```
personal.newAccount
eth.sendTransaction({from:eth.coinbase, to:eth.accounts[1], value: web3.toWei(100, "ether")})
```

轉帳100 Ether

```
eth.sendTransaction({from:eth.coinbase, to:eth.accounts[1], value: web3.toWei(100, "ether")})
```

之後查看網頁 :

> 因為我們使用的是 --dev 所以自動設定為Proof of Authority，所以產生交易後不需要手動挖礦即可生效

![](/assets/sdca.png)之後我們新增可以讀取區塊上資料，並且可以取得一個帳號的歷史交易紀錄之功能

```js
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import Web3 from 'web3';
const web3 = new Web3();
const eth = web3.eth;
const SHA3 = require('keccakjs')

class App extends Component {

  constructor() {
    super();
    this.state = {
      accounts: '',
      blockHeight: '',
      blocks: [],
      currentAddress: '',
      accountTransactions: []
    }
  }


  componentWillMount() {

    let h = new SHA3(256).update(Buffer.from("d6" + "94" + "d7c86c344ecbd9f166b053a32cd6cd34dda1b8af" + "02", 'hex')).digest('hex'); // 02  06 07 08
    let address = h.slice(-40) // 取後面四十個字

    console.log('-------------------------------------')
    console.log('地址Address: ')
    console.log(`0x${address}`)
    // https://ethereum.stackexchange.com/a/761

    web3.setProvider(new web3.providers.HttpProvider('http://localhost:8545')); //指定為RPC server的位置
    this.setState({ accounts: web3.eth.accounts });

    // 目前區塊高度
    let blockHeight = web3.eth.blockNumber;
    this.setState({ blockHeight });
    // 所有區塊資料
    let blocks = []
    for (let i = 0; i <= blockHeight; i++) {
      blocks.push(eth.getBlock(i, true));
    }
    this.setState({ blocks });
  }
  render() {
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to My Dapp</h2>
        </div>
        <div className="App-intro">
          {this.state.accounts.map((i, idx) => (
            <p key={idx}>帳號{idx}: {i}，餘額: {web3.fromWei(web3.eth.getBalance(i)).toString()} </p>
          ))}
        </div>
        查詢帳號交易紀錄:<input onChange={(e) => this.setState({currentAddress: e.target.value.trim()})} />
        <button onClick={() => this.getTransactions(this.state.currentAddress)}>查詢</button>


        <div>
          {this.state.accountTransactions.map(d => (
            <div>{JSON.stringify(d)}</div>
          ))}
        </div>
      </div>

    );
  }


  getTransactions(address) {
    let accountTransactions = [];
    this.state.blocks.forEach(block => {
      block.transactions.forEach(transaction => {
        if (transaction.from === address) {
          accountTransactions.push(transaction);
        }
      })
    })
    this.setState({ currentAddress: address })
    this.setState({ accountTransactions })
    return accountTransactions
  }
}

export default App;
```

# 常用指令

#### 初始化合約

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



