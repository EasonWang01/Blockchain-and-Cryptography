#### payable

```
function buyTicket(uint amount) payable {
}
```

包含payable的function才可以接受Ether

#### View, Pure, Constant

View: 可讀取，但不可修改

Pure: 不可讀取，不可修改

constant: 可讀取，但不可修改，不會加在function上，只加在變數上

> [http://solidity.readthedocs.io/en/develop/contracts.html\#view-functions](http://solidity.readthedocs.io/en/develop/contracts.html#view-functions)

#### 呼叫其他Contract

先部署Calculate contract 到鏈上

```
contract calculate {
    function add (int a, int b) returns (int) {
        return a + b
    }
}
```

假設其地址為 `0xfEDDF8DB160Dcb85f793bfEe734352760C4AB96a`

之後呼叫其contract

```go
contract computer {
    calculate calc = new calculate(0xfEDDF8DB160Dcb85f793bfEe734352760C4AB96a);
      function 2Add3() constant returns (int) {} {
        calc.add(2, 3);
    }
}
```

#### memory、storage、stack

> storage耗費最多Gas，memory耗費較少，stack免費。

Function參數預設均為memory，使用storage如下用法

```go
uint[] abc = [1, 2, 3];

function test(uint[] storage _abc) internal {
    _abc[0] = 0;
}

// abc變為[0, 2, 3]
```

預設選項

```
state variables 預設為 storage
function arguments 預設為 memory 
local variables 例如 struct, array , mapping type 預設為 storage
local variables 與 value type (除了 array, struct, mapping) 存在於 stack
```

[http://solidity.readthedocs.io/en/latest/frequently-asked-questions.html\#what-is-the-memory-keyword-what-does-it-do](http://solidity.readthedocs.io/en/latest/frequently-asked-questions.html#what-is-the-memory-keyword-what-does-it-do)

#### Fallback functions

如果呼叫的function不存在於智能合約上，則EVM會自動去執行fallback function

> 類似於匿名函數的寫法

```
function() {
     ....
}
```

[https://github.com/ConsenSys/Ethereum-Development-Best-Practices/wiki/Fallback-functions-and-the-fundamental-limitations-of-using-send\(\)-in-Ethereum-&-Solidity](https://github.com/ConsenSys/Ethereum-Development-Best-Practices/wiki/Fallback-functions-and-the-fundamental-limitations-of-using-send%28%29-in-Ethereum-&-Solidity)

#### Getter function

一般讀取資料可以直接把變數寫為public即可

```
uint[] public a;
```

如果想寫成function 記得要加上view或是pure關鍵字，告訴節點這是單純讀取固定資料的function，不然在例如Remix IDE之類不會直接顯示值出來。

```js
function showString() public pure returns(string) {
   return "test";
}
```

> 目前EVM不支援浮點數顯示，小於1且大於0的數均會顯示0

#### 確認地址是否存在

使用address\(0\)或是address\(0x0\)

範例：

```js
function transferOwnership(address newOwner) public onlyOwner {
  require(newOwner != address(0));
  OwnershipTransferred(owner, newOwner);
  owner = newOwner;
}
```

#### 型別與Gas關係

一般全域宣告變數uint與uint8耗用相同Gas，但如果是在struct裡面則有差別:

```go
struct NormalStruct {
  uint a;
  uint b;
  uint c;
}

struct MiniStruct {
  uint32 a;
  uint32 b;
  uint c;
}

// `MiniStruct` 可使用較少Gas
NormalStruct normal = NormalStruct(10, 20, 30);
MiniStruct mini = MiniStruct(10, 20, 30);
```

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
> [https://ethereum.stackexchange.com/questions/11556/use-string-type-or-bytes32](https://ethereum.stackexchange.com/questions/11556/use-string-type-or-bytes32)



