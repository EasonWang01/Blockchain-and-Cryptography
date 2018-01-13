## 型別

#### 1.Address

為 20 byte，通常用來存放讀取之Ethereum address。

具有如下方法可以使用

1.balance、transfer

```
address myAddress1 = 0xdf6d9ad96b630e06325ceb9f7e23a86695997421;
address myAddress2 = 0xdf6d9ad96b630e06325ceb9f7e23a86695997422;

if (myAddress1.balance < 10 && myAddress2.balance >= 10) myAddress1.transfer(10);
// myAddress1.transfer(10); 意思為從合約傳送金額到myAddress1
```



#### 2.Key-value對應

使用mapping 可產生一個類似Key-value對應的結構

```c
struct Person {
    bytes32 name;
    uint age;
}

mapping (address => Person) person;
```

`person` 為mapping類型，它的 key 是address 類型， value是Person 之struct類型

#### 3.Struct

```
struct Campaign {
    address beneficiary;
    uint fundingGoal;
    uint numFunders;
    uint amount;
}
```

然後

```
mapping (uint => Campaign) campaigns;  // 建立一個key為uint類型，value對應到Campaign struct之鍵值結構campaigns。

campaigns[campaignID] = Campaign(<address>, 100, 0, 0); // 使用Campaign(...)新增struct

Campaign storage c = campaigns[campaignID];  // 存取struct
uint amount = c.amount;
```

#### 4.Enum

```
enum fruits{ Apple, Banana, Beef, Chicken}
```

使用

```
fruits.Apple //回傳 0
fruits.Banana //回傳 1
```



