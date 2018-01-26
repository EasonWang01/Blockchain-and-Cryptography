## Solidity

本章節將介紹撰寫智能合約的語言：Solidity

[http://solidity.readthedocs.io/en/develop/solidity-in-depth.html](http://solidity.readthedocs.io/en/develop/solidity-in-depth.html)

我們可以使用Solidity網頁版IDE：[https://remix.ethereum.org](https://remix.ethereum.org) ，進行測試與部屬。

> 可參考官方style guide來撰寫：[http://solidity.readthedocs.io/en/develop/style-guide.html](http://solidity.readthedocs.io/en/develop/style-guide.html)

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
> 2.public為此function可被存取到的範圍
>
> 3.要寫出returns後面為此function會回傳的值以及型別

有關存取範圍可以有以下四種external, public, internal, private

```
external:  只可以給外面的contract使用，不可內部使用。

public:    都可呼叫，為預設。

internal:  跟private類似，但如果是繼承該contract的contract也可以使用。

private:   只有同contract才可使用。
```

#### 3.Modifiers

如果有共用的邏輯我們可以把它寫成modifier讓許多function共用

```
modifier onlyOwner {
    require(msg.sender == owner);
    _;
}
```

用法

```
function transferOwnership(address newOwner) onlyOwner { 
    owner = newOwner;
}
```

> \_ ; 的意思為擁有這個modifier的function會先跑完寫在 \_ ; 之前的東西才繼續該function

#### 4.Events

可以用來當特定事件發生時產生log，用來通知外界。

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

#### byte

[http://solidity.readthedocs.io/en/develop/types.html\#fixed-size-byte-arrays](http://solidity.readthedocs.io/en/develop/types.html#fixed-size-byte-arrays)

其return值通常為ASCII hex 碼，可以使用下面網站解碼 : [https://www.rapidtables.com/convert/number/hex-to-ascii.html](https://www.rapidtables.com/convert/number/hex-to-ascii.html)

在web3環境可以使用以下Function

```
web3.toAscii(填入Hex碼)
```

或是使用以下程式

```js
function hextoASCII(_hex) {
  let hex = _hex.toString();
  let str = '';
  for (let i = 0; i < hex.length; i += 2)
      str += String.fromCharCode(parseInt(hex.substr(i, 2), 16));
  return str;
}
```

> 有關要使用string 還是 byte 可參考: 
>
> https://ethereum.stackexchange.com/questions/11556/use-string-type-or-bytes32



