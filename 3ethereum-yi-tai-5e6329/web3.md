## Web3

官方Github : [https://github.com/ethereum/web3.js/](https://github.com/ethereum/web3.js/)

官方文件 : [https://web3js.readthedocs.io/en/1.0/web3.html](https://web3js.readthedocs.io/en/1.0/web3.html)

在使用者端的程式中，例如網頁，為了讀取區塊鏈的資料必須使用一些方法來存取，web3為一個可以存取Ethereum區塊鏈資料的第三方函式庫，使用之程式語言為Javascript，在Geth console中也可以直接使用。

> 另一個類似的專案為: [https://github.com/ethjs/ethjs](https://github.com/ethjs/ethjs)

Web3許多Function都可以寫為異步或同步執行，但在Metamask中大部分Function都只允許異步執行。

> [https://github.com/MetaMask/faq/blob/master/DEVELOPERS.md\#dizzy-all-async---think-of-metamask-as-a-light-client](https://github.com/MetaMask/faq/blob/master/DEVELOPERS.md#dizzy-all-async---think-of-metamask-as-a-light-client)

# 安裝

```
npm install web3

或是

yarn add web3
```

> 本書使用版本為`0.20.3`

## 使用

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
```

轉帳100 Ether

```
eth.sendTransaction({from:eth.coinbase, to:eth.accounts[1], value: web3.toWei(100, "ether")})
```

之後查看網頁 :

> 因為我們使用的是 --dev 所以自動設定為Proof of Authority，所以產生交易後不需要手動挖礦即可生效

![](/assets/sdca.png)之後我們新增可以讀取區塊上資料之功能。

> 先讀取出目前區塊的高度後，再去讀出每個區塊的內容。

```js
// 目前區塊高度
let blockHeight = web3.eth.blockNumber;
this.setState({ blockHeight });
// 所有區塊資料
let blocks = []
for (let i = 0; i <= blockHeight; i++) {
  blocks.push(eth.getBlock(i, true));
}
this.setState({ blocks });
```

過濾出特定地址之交易

```js
// 讀出特定地址之所有交易
getTransactions(address) {
  let accountTransactions = [];
  this.state.blocks.forEach(block => {
    block.transactions.forEach(transaction => {
      if (transaction.from === address || transaction.to === address) {
        accountTransactions.push(transaction);
      }
    })
  })
  this.setState({ currentAddress: address })
  this.setState({ accountTransactions })
  return accountTransactions
}
```

接著安裝我們使用到的相關UI套件:

> 使用Material UI: [https://material-ui-next.com](https://material-ui-next.com)

```
npm install material-ui@1.0.0-beta.30 material-ui-icons@1.0.0-beta.17 react-pure-css-modal
```

之後App.js完整程式如下

```js
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import Web3 from 'web3';
import { withStyles } from 'material-ui/styles';
import List, { ListItem, ListItemIcon, ListItemText, ListSubheader } from 'material-ui/List';
import Collapse from 'material-ui/transitions/Collapse';
import { ExpandLess, ExpandMore, MoveToInbox as InboxIcon, SwapHoriz } from 'material-ui-icons';
import { Modal } from 'react-pure-css-modal';

const styles = theme => ({
  root: {
    width: '100%',
    maxWidth: 760,
    margin: '0 auto',
  },
});

const web3 = new Web3();
window.web3 = web3
const eth = web3.eth;

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

  searchTransactionBtn() {
    document.getElementById('modal').click()
    this.getTransactions(this.state.currentAddress)
  }

  render() {
    const { classes } = this.props;
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to My Dapp</h2>
        </div>

        <br />查詢帳號交易紀錄 : <input onChange={(e) => this.setState({ currentAddress: e.target.value.trim() })} />
        <button onClick={() => this.searchTransactionBtn()}>查詢</button>

        <div className="App-intro">
          <br />
          {this.state.accounts.map((i, idx) => {
            let balance = web3.fromWei(web3.eth.getBalance(i)).toString();
            return (
              <div key={idx}>
                <br />
                <span>帳號{idx}: {i}，餘額: {balance.slice(0, 5)}{balance.indexOf('e') !== -1 ? 'e' : ''} </span>
                <span>( Ether )</span>
              </div>
            )
          }
          )}
        </div>


        <Modal id="modal" onClose={() => { console.log("Modal close") }}>

          {this.state.accountTransactions.length > 0
            ? <List className={classes.root}>

              {this.state.accountTransactions.map((d, idx) => (
                <div>
                  <ListItem button onClick={() => this.setState({ [`open${idx}`]: !this.state[`open${idx}`] })}>
                    <ListItemIcon>
                      <InboxIcon />
                    </ListItemIcon>
                    <ListItemText inset primary={d.hash} />
                    {this.state[`open${idx}`] ? <ExpandLess /> : <ExpandMore />}
                  </ListItem>
                  <Collapse component="li" in={this.state[`open${idx}`]} timeout="auto" unmountOnExit>
                    <List disablePadding>
                      <ListItem>
                        <ListItemIcon>
                          <SwapHoriz />
                        </ListItemIcon>
                        <ListItemText inset primary={`From: ${d.from}`} />
                      </ListItem>
                      <ListItem>
                        <ListItemText inset primary={`To: ${d.to}`} />
                      </ListItem>
                      <ListItem>
                        <ListItemText inset primary={`Gas Limit: ${d.gas}`} />
                        <ListItemText inset primary={`Gas Price: ${d.gasPrice}`} />
                        <ListItemText inset primary={`At Block: ${d.blockNumber}`} />
                        <ListItemText inset primary={`Value: ${d.value.toString()}`} />
                      </ListItem>
                    </List>
                  </Collapse>
                </div>
              ))}
            </List>
            : ''}
        </Modal>

      </div>

    );
  }


  // 讀出特定地址之所有交易
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

export default withStyles(styles)(App);
```

更改後可以看到目前畫面如下![](/assets/kopwe9.png)

> 更改App.css中的.App-header即可更換為自己喜好之上方背景圖片。
>
> ```css
> .App-header {
>   background-image: url(圖片路徑); 
>   height: 150px;
>   padding: 20px;
>   color: white;
> }
> ```

接著我們把帳號複製並且貼到輸入框中，然後點選查詢，即可看到其列出所有歷史交易

![](/assets/k;lsd.png)

點擊該筆交易後即可展開，並查看相關交易內容。

![](/assets/kpoasd0.png)

# 新增交易功能

我們接下來要讓兩個帳號之間可以使用網頁互相轉帳。

新增以下程式碼 :

```js
submitTransaction() {
    if (!this.state.senderAddress || !this.state.receiverAddress || !this.state.sendAmount) {
      alert('請輸入完再按確認。');
      window.location.reload();
      return
    }
    this.setState({ sending: true })
    let transaction = {
      from: this.state.senderAddress,
      to: this.state.receiverAddress,
      value: web3.toWei(this.state.sendAmount, "ether"),
      gas: 21000
    };
    if (!web3.isAddress(this.state.senderAddress)
      || !web3.isAddress(this.state.receiverAddress)) {
      alert("地址格式不正確");
      window.location.reload();
      return
    }
    // 解鎖發送交易的帳號
    web3.personal.unlockAccount(this.state.senderAddress); 
    // 發送交易
    eth.sendTransaction(transaction, function (err, result) {
      if (err) {
        alert(err);
        window.location.reload();
        return
      }
      alert(`交易成功，${result}`);
      window.location.reload();
    })
  }
```

在上面我們將使用`eth.sendTransaction` 發送交易，裡面填入相關發送人與接收人之地址以及交易金額，並且給他一個預設值Gas，並且記得在發送交易之前先解鎖帳號。

> UI部分的程式碼將不詳細敘述

完整 App.js程式碼如下 :

```js
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import Web3 from 'web3';
import { withStyles } from 'material-ui/styles';
import List, { ListItem, ListItemIcon, ListItemText, ListSubheader } from 'material-ui/List';
import Collapse from 'material-ui/transitions/Collapse';
import { ExpandLess, ExpandMore, MoveToInbox as InboxIcon, SwapHoriz, Send } from 'material-ui-icons';
import { Button, Icon, TextField, Input, InputAdornment } from 'material-ui';
import { Modal } from 'react-pure-css-modal';

const web3 = new Web3();
window.web3 = web3
const eth = web3.eth;

const styles = theme => ({
  root: {
    width: '100%',
    maxWidth: 760,
    margin: '0 auto',
  },
});

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

  searchTransactionBtn() {
    document.getElementById('searchModal').click();
    this.setState({ searchModal: true })
    this.getTransactions(this.state.currentAddress)
  }

  submitTransaction() {
    if (!this.state.senderAddress || !this.state.receiverAddress || !this.state.sendAmount) {
      alert('請輸入完再按確認。');
      window.location.reload();
      return
    }
    this.setState({ sending: true })
    let transaction = {
      from: this.state.senderAddress,
      to: this.state.receiverAddress,
      value: web3.toWei(this.state.sendAmount, "ether"),
      gas: 21000
    };
    if (!web3.isAddress(this.state.senderAddress)
      || !web3.isAddress(this.state.receiverAddress)) {
      alert("地址格式不正確");
      window.location.reload();
      return
    }
    // 解鎖發送交易的帳號
    web3.personal.unlockAccount(this.state.senderAddress);
    // 發送交易
    eth.sendTransaction(transaction, function (err, result) {
      if (err) {
         alert(err);
         window.location.reload();
         return
      }
      alert(`交易成功，${result}`);
      window.location.reload();
    })
  }

  // 讀出特定地址之所有交易
  getTransactions(address) {
    let accountTransactions = [];
    this.state.blocks.forEach(block => {
      block.transactions.forEach(transaction => {
        if (transaction.from === address || transaction.to === address) {
          accountTransactions.push(transaction);
        }
      })
    })
    this.setState({ currentAddress: address })
    this.setState({ accountTransactions })
    return accountTransactions
  }

  render() {
    const { classes } = this.props;
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to My Dapp</h2>
        </div>

        <br />查詢帳號交易紀錄 : <input onChange={(e) => this.setState({ currentAddress: e.target.value.trim() })} />
        <button onClick={() => this.searchTransactionBtn()}>查詢</button>

        <div className="App-intro">
          <br />
          {this.state.accounts.map((i, idx) => {
            let balance = web3.fromWei(web3.eth.getBalance(i)).toString();
            return (
              <div key={idx}>
                <br />
                <span>帳號{idx}: {i}，餘額: {balance.slice(0, 5)}{balance.indexOf('e') !== -1 ? 'e' : ''} </span>
                <span>( Ether )</span>
              </div>
            )
          }
          )}
        </div>

        <Modal id="searchModal" onClose={() => { this.setState({ searchModal: false }) }}>
          {this.state.accountTransactions.length > 0
            ? <List className={classes.root}>
              {this.state.accountTransactions.map((d, idx) => (
                <div>
                  <ListItem button onClick={() => this.setState({ [`open${idx}`]: !this.state[`open${idx}`] })}>
                    <ListItemIcon>
                      <InboxIcon />
                    </ListItemIcon>
                    <ListItemText inset primary={d.hash} />
                    {this.state[`open${idx}`] ? <ExpandLess /> : <ExpandMore />}
                  </ListItem>
                  <Collapse component="li" in={this.state[`open${idx}`]} timeout="auto" unmountOnExit>
                    <List disablePadding>
                      <ListItem>
                        <ListItemIcon>
                          <SwapHoriz />
                        </ListItemIcon>
                        <ListItemText inset primary={`From: ${d.from}`} />
                      </ListItem>
                      <ListItem>
                        <ListItemText inset primary={`To: ${d.to}`} />
                      </ListItem>
                      <ListItem>
                        <ListItemText inset primary={`Gas Limit: ${d.gas}`} />
                        <ListItemText inset primary={`Gas Price: ${d.gasPrice}`} />
                        <ListItemText inset primary={`At Block: ${d.blockNumber}`} />
                        <ListItemText inset primary={`Value: ${d.value.toString()}`} />
                      </ListItem>
                    </List>
                  </Collapse>
                </div>
              ))}
            </List>
            : ''}
        </Modal>

        <Modal id="send_modal" onClose={() => { this.setState({ sendModal: false }) }}>
          <div>
            <TextField
              id="send_from"
              label="發送人"
              placeholder="帳號"
              margin="normal"
              onChange={(e) => this.setState({ senderAddress: e.target.value })}
            />
            <br />
            <TextField
              id="send_from"
              label="接收人"
              placeholder="帳號"
              margin="normal"
              onChange={(e) => this.setState({ receiverAddress: e.target.value })}
            />
            <br />
            <br />
            <Input
              type="number"
              id="adornment-amount"
              value={this.state.amount}
              onChange={(e) => this.setState({ sendAmount: e.target.value })}
              startAdornment={<InputAdornment position="start">$</InputAdornment>}
            />
            <br />
            <br /><br />
            <Button onClick={() => this.submitTransaction()} raised color="default">
              <Send />
              {this.state.sending ? '請稍等...' : '發送'}
            </Button>

          </div>
        </Modal>

        {
          this.state.sendModal || this.state.searchModal
            ?
            ''
            :
            <div style={{ marginTop: '50px' }}>
              <Button onClick={() => {
                this.setState({ sendModal: true })
                document.getElementById('send_modal').click();
              }} raised color="primary">
                <SwapHoriz />發送交易
             </Button>
            </div>
        }
      </div >
    );
  }
}

export default withStyles(styles)(App);
```

接著可以看到網頁下方有一個發送交易按鈕，我們點擊它

![](/assets/xow.png)之後會彈出一個可以輸入相關交易資訊的方塊，我們填入相關帳號以及金額

![](/assets/ca9.png)

之後按下發送按鈕，即可看到上方出現交易成功字樣。![](/assets/kcsd9.png)

我們使用剛才的交易搜尋功能，即可看到剛才我們轉帳的這筆交易內容

![](/assets/jljwe99.png)

