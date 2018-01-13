# 繼承（Inheritance）、虛擬合約（Abstract Contracts）、介面（Interfaces）



#### 1.繼承（Inheritance）

使用 `is` 來繼承之前寫的contract

範例如下：

```js
contract man {
  function man() { owner = msg.sender; } // 建構子，和contract同名，一開始即會執行
}

contract superman is man {
   function fly() {
     if (msg.sender == owner) selfdestruct(owner); //可以存取繼承的contract變數owner
   }
}
```

> 繼承的contract可以存取所有被繼承的contract的東西\(不包含private\)

#### 2.虛擬合約（Abstract Contracts）

類似於繼承contract，不過在被繼承的contract不會實作邏輯內容

```
contract bird {
    function fly() public returns (bytes32);
}

contract Flybird is bird {
    function fly() public returns (bytes32) { return "miaow"; }
}
```

#### 3.介面（Interfaces）





