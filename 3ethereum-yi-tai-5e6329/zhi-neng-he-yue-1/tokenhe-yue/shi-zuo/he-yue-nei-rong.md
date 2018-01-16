# 合約說明

1.首先我們會在 ERC20\_token.sol 檔案，引入另一個 ERC20\_interface.sol 檔案。

```js
import "./ERC20_interface.sol"; //引入interface

contract ERC20_token is ERC20_interface { // 使用 is 繼承
   .....
}
```

2.再來我們定義會用到的全域變數

```js
//避免超過產生overflow
uint256 constant private MAX_UINT256 = 2 ** 256 - 1; 

// 查詢特定地址的餘額，類似balances[address]
mapping(address => uint256) public balances; 
// 查詢特定地址可以給另一個地址的轉帳配額，類似：allowed[from_address][to_address]
mapping(address => mapping(address => uint256)) public allowed; 

string public name;         // 合約名稱
uint8 public decimals = 18; // 小數點，官方建議為18。因為EVM目前無法處理浮點數，所以用此方式，把一定位數的整數0當作小數點
string public symbol;       // 一個代表合約的符號 e.g. ^_^
address owner;              // 存放合約擁有者地址
uint256 public buyPrice;    // 一單位Ether可以換多少token
uint private weiToEther = 10 ** 18; // 把單位從wei轉為Ether，因為msg.value預設是以wei為單位。
```

3.接著寫出此合約的建構子 \( 合約部署即會執行一次 \)，以及共用的Modifyer。

> 之後我們要部署合約時會需要輸入四個參數：\_initialSupply、\_buyPrice、\_tokenName、\_tokenSymbol。

```js
// 建構子，一開始即會執行，需要提供總量、價格、名稱、標誌
function ERC20_token(
  uint256 _initialSupply,
  uint256 _buyPrice,
  string _tokenName,
  string _tokenSymbol
) public {
  totalSupply = _initialSupply * 10 ** uint256(decimals); // token總量
  balances[msg.sender] = totalSupply; // 設定合約token擁有者為合約部屬者

  name = _tokenName;     // token名稱
  symbol = _tokenSymbol; // token 標誌
  owner = msg.sender;    // 合約擁有人
  buyPrice = _buyPrice;  // 每單位 ether 之價格
}

// 限定只有合約部屬人才能執行特定function
modifier onlyOwner() {
  require(msg.sender == owner);
  _;
}
```

4.然後寫出ERC-20必要定義的Function

```js
// 查詢餘額
function balanceOf(address _owner) public view returns (uint256 balance) {
  return balances[_owner];
}

// 從合約擁有人地址轉帳
function transfer(address _to, uint256 _value) public returns(bool success) {
  require(balances[msg.sender] >= _value);
  balances[msg.sender] -= _value;
  balances[_to] += _value;
  Transfer(msg.sender, _to, _value);
  return true;
}

// 從某一人地址轉給另一人地址，需要其轉帳配額有被同意，可想像為小明(msg.sender)用爸爸的副卡(_from)轉帳給別人(_to)
function transferFrom(address _from, address _to, uint256 _value) public returns(bool success) {
  uint256 allowance = allowed[_from][msg.sender];
  require(balances[_from] >= _value && allowance >= _value);
  balances[_to] += _value;
  balances[_from] -= _value;
  if (allowance < MAX_UINT256) {
    allowed[_from][msg.sender] -= _value;
  }
  Transfer(_from, _to, _value);
  return true;
}

// 給予特定帳號轉帳配額 類似小明的爸爸(msg.sender)給小明(_spender)一張信用卡副卡，額度為value
function approve(address _spender, uint256 _value) public returns(bool success) {
  allowed[msg.sender][_spender] = _value;
  Approval(msg.sender, _spender, _value);
  return true;
}

// 查詢特定帳號轉給另一帳號之轉帳配額
function allowance(address _owner, address _spender) public view returns (uint256 remaining) {
  return allowed[_owner][_spender];
}
```

5.讓其他帳號可以通過傳送Ether給此智能合約，來取得Token

```js
// 設定Token購買價格，只有合約部屬者可以設定
function setPrice(uint _price) public onlyOwner {
  buyPrice = _price;
}

// 購買Token，payable代表此Function可以接受Ether
function buy() public payable {
  uint amount;
  amount = (msg.value / weiToEther) * buyPrice * 10 ** uint256(decimals); // 購買多少token
  require(balances[owner] >= amount); // 檢查還有沒有足夠token可以賣
  balances[msg.sender] += amount; // 增加購買者token
  balances[owner] -= amount; // 減少擁有者token
  Transfer(owner, msg.sender, amount); // 產生token轉帳log
}

// 從合約轉出Ether到部屬者帳戶
function withdraw(uint amount) public onlyOwner {
  owner.transfer(amount * weiToEther);
}
```

6.最後讓此合約可以從區塊鏈上移除，並將合約地址上的Ether轉帳給合約的部屬者。

```js
// 從區塊鏈上移出合約
function deleteContract() public onlyOwner {
    selfdestruct(owner); // 將合約剩餘的Ether轉給owner
 }
```



