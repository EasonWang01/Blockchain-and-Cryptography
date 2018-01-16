# ERC-20 token 合約 {#7141}

[https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md)

目的是讓以太坊上與Token有關的合約都有共同的介面與功能。而Token可以想像是類似湯姆熊遊樂園等地方，可以使用代幣來進行遊戲，而每個智能合約上之Token也會等價對應於某一件事物，而持有Token的人在區塊鏈上會用地址來表示，也就是某個地址擁有多少個Token。

Token與以太幣是沒有直接關係的，因為Token是每個智能合約的撰寫者所寫出來的邏輯，但通常想要獲取某個Token通常會需要傳送一定量的以太幣到某個智能合約的地址，然後合約將會記錄每個傳過來的地址與金額，並給予其Token。

下面我們將實際撰寫一個符合ERC-20標準的Token合約，讓讀者可以更好的了解其概念。

## 規範 :

可直接存取常數\(Optional\)

```
string public constant name = "Token Name"; // 合約Token的名稱
string public constant symbol = "AAA";  // 合約符號
uint8 public constant decimals = 18;  // Token小數點，例如totalSupply = tokensIActuallyWant * (10 ^ decimals)
```

```js
contract ERC20Interface {
  function totalSupply() public constant returns (uint);  
  // 查詢合約共有多少個Token

  function balanceOf(address tokenOwner) public constant returns (uint balance); 
  // 查詢特定address所含的Token數量

  function allowance(address tokenOwner, address spender) public constant returns (uint remaining);
  // 查詢某一個地址開放給另一個地址可以提款之Token限額

  function transfer(address to, uint tokens) public returns (bool success);   
  // 給予某個地址指定數量之Token

  function approve(address spender, uint tokens) public returns (bool success);
  // 允許第一個參數的地址可以從你的地址轉帳特定限額的Token到他的地址。

  function transferFrom(address from, address to, uint tokens) public returns (bool success);
  // 從第一個參數的地址傳送指定數量的Token給第二個參數地址

  event Transfer(address indexed from, address indexed to, uint tokens); 
  // 當有觸發transfer function時一定要觸發此事件

  event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
  // 當有觸發approve function時一定要觸發此事件
}
```



