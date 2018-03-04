## Solidity

本章節將介紹撰寫智能合約的語言：Solidity

[http://solidity.readthedocs.io/en/develop/solidity-in-depth.html](http://solidity.readthedocs.io/en/develop/solidity-in-depth.html)

我們可以使用Solidity網頁版IDE：[https://remix.ethereum.org](https://remix.ethereum.org) ，進行測試與部屬。

> 可參考官方style guide來撰寫：[http://solidity.readthedocs.io/en/develop/style-guide.html](http://solidity.readthedocs.io/en/develop/style-guide.html)

#### 安裝Solidity Compiler :

[http://solidity.readthedocs.io/en/latest/installing-solidity.html](https://www.gitbook.com/book/easonwang01/e/edit#)

> 如果使用Remix IDE網頁則不需要安裝。

使用Node.js套件：[https://github.com/ethereum/solc-js](https://github.com/ethereum/solc-js)

```
安裝：npm install solc -g
執行：solcjs --help
```

# 合約撰寫

#### 1.版本

```
pragma solidity ^0.4.20;
```

一開始在程式最上面會指定使用之版本。

#### 2.合約名稱

```
contract TestContract {
 .....
}
```

每個合約會用合約名稱加上大括號 { } 包住。

#### 3.引入其他合約

```
import "filename";
import "filename" as symbolName;
import * as symbolName from "filename";
import {symbol1 as alias, symbol2} from "filename";
```

以上四種方式都可以。

#### 4.註解

```
// ...
/* ...  */
```

與Javascript類似，分別為單行註解與多行註解。

# 合約內容

#### 1.宣告變數

首先我們會把需要用到的全域變數宣告在最上面

```js
contract TestContract {
    uint apple_num;
    uint banana_num;  
}
```

#### 2.Function

function寫法如下

```
function run(string person) public returns (uint step) { 
    // ...
}
```

> 1.參數要包含型別
>
> 2.public 為此 function 可被存取到的範圍
>
> 3.如果Function 有 return值的話，要寫出 returns 後面接上 function 回傳值的型別

有關存取範圍可以有以下四種external, public, internal, private

```
external:  只可以給外面的contract使用，不可內部使用。

public:    都可呼叫，為預設。

private:   只有同contract才可使用。

internal:  跟private類似，但如果是繼承該contract的contract也可以使用。
```

#### 3.Modifiers

如果有共用的邏輯我們可以把它寫成modifier讓許多function共用

```
modifier onlyOwner {
    require(msg.sender == owner);
    _;
}
```

> \_ ; 的意思為擁有這個modifier的function會先執行完寫在 modifier  \_ ; 之前的東西，之後才會繼續其本身Function的執行。

用法：在 Function 最後面加上 modifier 的名稱

```
function transferOwnership(address newOwner) onlyOwner { 
    owner = newOwner;
}
```

#### 4.Events

可以在特定事件發生時產生log，用來通知外界。

```
event NewOffer(address sender_, uint amount); // Event
```

使用\(寫在function內\)

```
function bid() public payable {
    // ...
    NewOffer(msg.sender, msg.value); // 觸發事件
}
```

> 通常用大寫命名

之後在web3可用如下方式取得事件:

```js
ContractInstance = web3.eth.contract(Contract.ABI).at(.address);
const eventHandler = ContractInstance.allEvents((error, event) => {
  if (error) {
    throw error
  }
  console.log('Event:');
  console.log(event);
});

// 或是

const event = ContractInstance.事件名稱({}, {fromBlock: 0, toBlock: 'latest'}, function (error, result) {
  if (!error)
  console.log(result)
});

// 其中第一個參數 {} 作用類似Filter可參考: https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethfilter
```

之後存取時裡面會有一個`args` 的key其value及為我們當時傳入Event的參數。

[http://solidity.readthedocs.io/en/develop/contracts.html\#events](http://solidity.readthedocs.io/en/develop/contracts.html#events)

[https://ethereum.stackexchange.com/questions/16313/how-can-i-view-event-logs-for-an-ethereum-contract](https://ethereum.stackexchange.com/questions/16313/how-can-i-view-event-logs-for-an-ethereum-contract)

#### 



