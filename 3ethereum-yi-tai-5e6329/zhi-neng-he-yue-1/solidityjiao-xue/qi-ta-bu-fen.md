# payable

```
function buyTicket(uint amount) payable {
}
```

包含payable的function才可以接受Ether

# View, Pure, Const

View: 可讀取，但不可修改

Pure: 不可讀取，不可修改

const: 可讀取，但不可修改，不可加在function上

# 呼叫其他Contract

先部署Calculate contract 到鏈上

```
contract calculate {
  function add (int a, int b) returns (int) {
    return a + b
  }
}
```

假設其地址為0xfEDDF8DB160Dcb85f793bfEe734352760C4AB96a

之後呼叫其contract

```
contract computer {
  calculate calc = new calculate(0xfEDDF8DB160Dcb85f793bfEe734352760C4AB96a);
  function 2Add3() constant returns (int) {} {
    calc.add(2, 3);
  }
}
```

# memory/storage

類似pass by value 與 pass by reference

預設均為memory，使用storage如下用法

```
uint[] abc = [1, 2, 3];

function test(uint[] storage _abc) internal {
    _abc[0] = 0;
}

// abc變為[0, 2, 3]
```

> function只能為internal或private

# Fallback functions

如果呼叫的function不存在於智能合約上，則EVM會自動去執行fallback function

> 類似於匿名函數的寫法

```
function() {
     ....
}
```

[https://github.com/ConsenSys/Ethereum-Development-Best-Practices/wiki/Fallback-functions-and-the-fundamental-limitations-of-using-send\(\)-in-Ethereum-&-Solidity](https://github.com/ConsenSys/Ethereum-Development-Best-Practices/wiki/Fallback-functions-and-the-fundamental-limitations-of-using-send%28%29-in-Ethereum-&-Solidity)

# Getter function

一般讀取資料可以直接把變數寫為public即可

```
uint[] public a;
```

如果想寫成function 記得要加上constant關鍵字，告訴節點這是單純讀取固定資料的function，不然在例如Remix IDE之類不會直接顯示值出來。

```js
function cc() public constant returns(string) {
   return "test";
}
```

> 目前EVM不支援浮點數顯示，小於1大0的數均會顯示0



