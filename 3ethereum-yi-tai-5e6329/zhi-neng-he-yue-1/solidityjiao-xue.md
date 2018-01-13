## Solidity

本章節將介紹撰寫智能合約的語言：Solidity

[http://solidity.readthedocs.io/en/develop/solidity-in-depth.html](http://solidity.readthedocs.io/en/develop/solidity-in-depth.html)

我們可以使用Solidity網頁版IDE：[https://remix.ethereum.org，進行測試與部屬。](https://remix.ethereum.org，進行測試與部屬。)

#### 1.版本

```
pragma solidity ^0.4.0;
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

與Javascript類似，單行註解與多行註解。

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
> 2.public為此function可被存取到的範圍，將在後面章節介紹
>
> 3.要寫出returns後面為此function會回傳的值以及型別

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
event newOffer(address sender_, uint amount); // Event
```

使用\(寫在function內\)

```
function bid() public payable {
    // ...
    newOffer(msg.sender, msg.value); // 觸發事件
}
```



