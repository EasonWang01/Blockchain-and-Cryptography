# Dapp實作

我們一樣先啟動Geth的開發用節點：

```
geth --dev --rpc --rpcport 8545 --datadir ./eth_Dapp --rpccorsdomain="*"  console
```

> 如果出現database already contains an incompatible genesis block，換一個--datadir 路徑即可

然後一樣打開[http://remix.ethereum.org/](http://remix.ethereum.org/)

我們這次來做一個會員管理系統，其中包含會員新增，會員簽到，會員移除與會員升級等功能。

一樣在Remix IDE中填入以下程式：

```go
pragma solidity ^0.4.19;

contract HonestClub {

    address public clubMaster;
    Member[] public members;

    // 會員的資料內容  
    struct Member {
        string name;
        string email; 
        bool signIn;
        uint8 level;
        uint registerTime;
    }

    function HonestClub () public {
        clubMaster = msg.sender;
    }

    modifier onlyMaster() {
        require(msg.sender == clubMaster);
        _;
    }
    modifier enoughMember(uint id) {
        require(id <= members.length - 1);
        _;
    }
    // 新增會員
    function add_member(string name, string email) public onlyMaster {
        require(members.length < 20);
        members.push(Member(name, email, false, 1, now));
    }
    // 查詢會員數量
    function how_many_members() view public returns(uint) {
        return members.length;
    }
    // 移除會員
    function remove_member(uint8 id) public onlyMaster enoughMember(id) {
        while (id < members.length - 1) {
            members[id] = members[id + 1];
            id++;
        }
        members.length--;
    }
    // 會員等級調整
    function upgrade_member(uint8 id, uint8 num) public onlyMaster
      enoughMember(id) {
        require(num <= 5); // 最高等級為五
        members[id].level = num;
    }
    // 進行簽到
    function signIn(uint8 id) public onlyMaster enoughMember(id) {
        members[id].signIn = true;
    }
    // 重置所有簽到
    function reset_signIn() public onlyMaster {
        uint8 id = 0;
        while (id <= members.length - 1) {
            members[id].signIn = false;
            id++;
        }
    }

}
```

> 1.上面使用到了now關鍵字，但不建議在關鍵地方使用，因為其可能被修改，可參考以下連結之下方：
>
> [http://solidity.readthedocs.io/en/develop/units-and-global-variables.html\#block-and-transaction-properties](http://solidity.readthedocs.io/en/develop/units-and-global-variables.html#block-and-transaction-properties)
>
> 2.我們使用while來遍歷陣列來修改會員，但如果陣列長度過長會很耗費Gas，而目前因為還沒開放將Memory Array直接賦予運算後的值給Storage陣列，所以要注意陣列長度避免耗費過多Gas。

我們一樣使用`create-react-app` 快速建立一個Web模板。

```
create-react-app honestClub
cd honestClub
```

再來安裝我們會用到第三方模組：

> 在以下安裝的模組中，除了web3以外，其他均為UI相關模組

```
npm install web3@0.20.4  semantic-ui-react@0.77.2 semantic-ui-css@2.2.14 react-pure-css-modal
```

安裝後執行程式：

```
npm start
```

#### 加入Mock Data 與 畫面

在連接上區塊鏈之前我們先把測試資料以及UI畫面寫好。

新增一個`mockData` 資料夾在`/src` 路徑下，裡面新增一個`index.js`檔案，用來存放我們的前期測試資料，在`index.js`檔案填入如下：

```js
export default [{
  signIn: true,
  name: 'John Lilki',
  registerTimestamp: 1517318657071,
  email: 'jhlilk22@yahoo.com',
  level: 1
}, {
  signIn: true,
  name: 'Jamie Harington',
  registerTimestamp: 1517318627071,
  email: 'jamieharingonton@hotmail.com',
  level: 3
}, {
  signIn: true,
  name: 'Jill Lewis',
  registerTimestamp: 1517118657071,
  email: 'Jill Lewis@gmail.com',
  level: 2
}, {
  signIn: false,
  name: 'Goo Adam',
  registerTimestamp: 1517318657071,
  email: 'aasgoo@gmail.com',
  level: 1
}, {
  signIn: false,
  name: 'Re mas',
  registerTimestamp: 1517318237071,
  email: 'remas@gmail.com',
  level: 1
}, {
  signIn: true,
  name: 'Banana goda',
  registerTimestamp: 1517312237071,
  email: 'bananago@gmail.com',
  level: 2
}]
```

再來我們到App.js中，把程式改為如下

```js
import React, { Component } from 'react';
import './App.css';
import Web3 from 'web3';
import { Button, Checkbox, Icon, Table, Label, Menu, Rating, Input } from 'semantic-ui-react'
import { Modal } from 'react-pure-css-modal';
import 'semantic-ui-css/semantic.min.css';
import mockData from './mockData/'

class App extends Component {
  constructor() {
    super();
    this.state = {
      activePage: '',
      mockData,
    }
  }
  componentWillMount() {
  }

  handlePageClick = (e, { page }) => {
    this.setState({ activePage: page })
  }

  addMember() {
  }

  upgradeMember() {
  }

  render() {
    return (
      <div className="App">
        <h1 style={{ marginTop: "30px" }}>會員管理</h1>
        <div style={{ width: '70%', margin: '0 auto', marginTop: "30px" }}>
          <Table celled definition >
            <Table.Header>
              <Table.Row>
                <Table.HeaderCell>簽到</Table.HeaderCell>
                <Table.HeaderCell>ID</Table.HeaderCell>
                <Table.HeaderCell>名稱</Table.HeaderCell>
                <Table.HeaderCell>註冊日期</Table.HeaderCell>
                <Table.HeaderCell>E-mail</Table.HeaderCell>
                <Table.HeaderCell>會員等級</Table.HeaderCell>
              </Table.Row>
            </Table.Header>

            <Table.Body>
              {
                this.state.mockData.map((member, idx) => (
                  <Table.Row>
                    <Table.Cell collapsing>
                      <Checkbox onChange={() => console.log('changed')} slider defaultChecked={member.signIn} />
                    </Table.Cell>
                    <Table.Cell>{idx}</Table.Cell>
                    <Table.Cell>{member.name}</Table.Cell>
                    <Table.Cell>{new Date(member.registerTimestamp).toString()}</Table.Cell>
                    <Table.Cell>{member.email}</Table.Cell>
                    <Table.Cell> <Rating disabled icon='star' defaultRating={member.level} maxRating={5} /></Table.Cell>
                  </Table.Row>
                ))
              }
            </Table.Body>

            <Table.Footer fullWidth>
              <Table.Row>
                <Table.HeaderCell />
                <Table.HeaderCell colSpan='6'>
                  <Button onClick={() => document.getElementById('upgradeMember').click()} color="yellow" floated='right' icon labelPosition='left' size='small'>
                    <Icon name='user' /> 升級會員
                  </Button>
                  <Button style={{ marginLeft: '20px' }} onClick={() => document.getElementById('addMember').click()} color="teal" floated='right' icon labelPosition='left' size='small'>
                    <Icon name='user' /> 新增會員
                  </Button>

                  <Menu activeIndex={2} pagination>
                    <Menu.Item icon>
                      <Icon name='left chevron' />
                    </Menu.Item>
                    {
                      [1, 2, 3, 4].map(item => (
                        <Menu.Item as="a" page={item} active={this.state.activePage === item} onClick={this.handlePageClick}>{item}</Menu.Item>
                      ))
                    }
                    <Menu.Item icon>
                      <Icon name='right chevron' />
                    </Menu.Item>
                  </Menu>
                </Table.HeaderCell>
              </Table.Row>
            </Table.Footer>
          </Table>
        </div>

        <Modal id="addMember">
          <h2 style={{ marginTop: '25px' }}>新增會員</h2>
          <div style={{ marginTop: '50px' }}>
            <Input onChange={(e) => this.setState({ addUser_name: e.target.value })} placeholder='姓名' /><br /><br />
            <Input onChange={(e) => this.setState({ addUser_email: e.target.value })} placeholder='Email' /><br /><br />
            <Button onClick={() => this.addMember()} color='teal'>新增</Button>
          </div>
        </Modal>
        <Modal id="upgradeMember" onClose={() => { console.log("upgradeMember Modal close") }} >
          <h2 style={{ marginTop: '25px' }}>升級會員</h2>
          <div style={{ marginTop: '50px' }}>
            <Input onChange={(e) => this.setState({ upgradeUser_ID: e.target.value })} placeholder='ID' /><br /><br />
            <Input onChange={(e) => this.setState({ upgradeUser_level: e.target.value })} placeholder='等級' /><br /><br />
            <Button onClick={() => this.upgradeMember()} color='yellow'>升級</Button>
          </div>
        </Modal>
      </div>
    );
  }
}

export default App;
```

目前可看到如下畫面：

## ![](/assets/螢幕快照 2018-01-31 下午12.45.43.png)



