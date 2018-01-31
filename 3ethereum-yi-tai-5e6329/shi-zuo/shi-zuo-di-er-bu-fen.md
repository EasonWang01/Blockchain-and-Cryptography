## 部署合約

在一開始我們已經啟動了Geth的節點以及寫好了合約的程式在Remix IDE上，接下來我們要將合約從Remix IDE部署到本地Geth節點上。

接著點擊Remix IDE上的Web3 Provider，然後點選Create。

![](/assets/螢幕快照 2018-01-31 上午11.26.54.png)再來點擊上方的Compile Tab然後按下Detail，並且複製下ABI與剛才合約部署的地址。

![](/assets/螢幕快照 2018-01-31 上午11.28.45.png)

#### 貼上合約內容到程式中

我們回到剛才網頁程式中，開一個資料夾名為Contract，放在src路徑下，裡面新增index.js。

/src/Contract/index.js內容：

```js
export default {
  ABI: 填上剛複製的ABI
  address: "填上合約地址"
}
```

然後在App.js中引入，並且建立實例。

```js
import clubContract from './Contract'

const web3 = new Web3();
window.web3 = web3;
const Contract = web3.eth.contract(clubContract.ABI).at(clubContract.address);
window.Contract = Contract;
```

再來指定RPC server位置

```js
componentWillMount() {
  web3.setProvider(new web3.providers.HttpProvider('http://localhost:8545')); //指定為RPC server的位置
}
```

新增會員

```js
addMember() {
    if(!this.state.addUser_name || !this.state.addUser_email) {
      alert('請先輸入會員名稱與Email');
      return
    }
    Contract.add_member(this.state.addUser_name, this.state.addUser_email,
      { from: web3.eth.accounts[0], gas: 132700 }
    ,function(err, result){
      if(err) console.log(err);
      console.log(result);
      this.setState({
        addUser_name: '',
        addUser_email: ''
      })
    });
}
```



