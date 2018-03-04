## 型別

#### 1.Address

為 20 byte，通常用來存放讀取之Ethereum address。

具有如下方法可以使用

**1.balance、transfer**

> balance可以取得地址的餘額
>
> transfer可以傳送金額到該地址
>
> \(單位均為wei\)

```js
address myAddress1 = 0xdf6d9ad96b630e06325ceb9f7e23a86695997421;
address myAddress2 = 0xdf6d9ad96b630e06325ceb9f7e23a86695997422;

if (myAddress1.balance < 10 && myAddress2.balance >= 10) myAddress1.transfer(10);
```

> `myAddress1.transfer(10);` 意思為從合約傳送金額到 myAddress1

**2.msg.sender**

預設為執行合約 Function 人的地址。

**3.call**

呼叫特定合約的Function

```go
contractAddress.call(bytes4(keccak256("functionName(args)")))
```

> 可以加入要提供的 Gas 以及 Ether

https://ethereum.stackexchange.com/a/8281/30691

[https://vomtom.at/address-call-vs-delegatecall-vs-libraries/](https://vomtom.at/address-call-vs-delegatecall-vs-libraries/)

#### 2.Key-value對應

使用mapping 可產生一個類似Key-value對應的結構

```c
struct Person {
    bytes32 name;
    uint age;
}

mapping (address => Person) person;
```

`person` 為mapping類型，它的 key 是address 類型， value 是Person 之 struct 類型。

> 使用方法為 : e.g. person\[地址\]

#### 3.Struct

```go
struct Campaign {
    address beneficiary;
    uint fundingGoal;
    uint numFunders;
    uint amount;
}
```

相關操作

```go
mapping (uint => Campaign) campaigns;  // 建立一個key為uint類型，value對應到Campaign struct之鍵值結構campaigns。

campaigns[campaignID] = Campaign(<address>, 100, 0, 0); // 使用Campaign(...參數)，新增struct

Campaign storage c = campaigns[campaignID];  // 把campaigns[campaignID] 讀取出的Campaign struct給變數c

uint amount = c.amount; // 存取struct中的值
```

#### 4.Enum

```java
enum fruits{ Apple, Banana, Beef, Chicken}
```

使用

```
fruits.Apple //回傳 0
fruits.Banana //回傳 1
```

也可以用來當作階段判斷：

```java
// 階段列表
enum Stages {
  AcceptingBlindedBids,
  RevealBids,
  AnotherStage,
  AreWeDoneYet,
  Finished
}

// 初始化階段
Stages public stage = Stages.AcceptingBlindedBids;

// 更改階段
function nextStage() internal {
  stage = Stages(uint(stage) + 1);
}

// 判斷階段為何
if (stage == Stages.AcceptingBlindedBids){
  ...
}
```

#### 5.Array

```go
uint[] myArray;
function Test() constant returns (uint[]) {
    myArray.push(123); 
    return myArray;
}
```

或是使用memory Array \( 宣告長度為 3 的 Array \)

```js
uint[] memory values = new uint[](3);
```

我們也可以在Array裡面放入Struct

```js
struct A_Struct {
    uint field1;
    uint field2;
}

A_Struct[] public myStructs;

function pushStruct() public {
    A_Struct memory m;
    m.field1 = 1;
    m.field2 = 2;
    myStructs.push(m);

    // 或是直接使用 myStructs.push(1, 2);
}
```

[http://solidity.readthedocs.io/en/develop/types.html\#allocating-memory-arrays](http://solidity.readthedocs.io/en/develop/types.html#allocating-memory-arrays)

移除Array元素

```
delete myStructs[2];
```

清空Array

```
myStructs.length = 0;
```

#### 6.Boolean

與其他程式語言用法相同

```
!  (logical negation)
&& (logical conjunction, “and”)
|| (logical disjunction, “or”)
== (equality)
!= (inequality)
```

#### 7.Integer

與其他程式語言用法相同，包含 sign 和 unsign \( int / uint \)，範圍從8到256， e.g. `uint8, uint16, uint 32 ... uint256`

> 目前 Ethereum 尚未支援浮點數運算



