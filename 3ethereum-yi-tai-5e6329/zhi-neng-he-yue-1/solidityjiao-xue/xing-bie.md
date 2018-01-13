## 型別

#### 1.Address

為 20 byte，通常用來存放讀取之Ethereum address。

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



