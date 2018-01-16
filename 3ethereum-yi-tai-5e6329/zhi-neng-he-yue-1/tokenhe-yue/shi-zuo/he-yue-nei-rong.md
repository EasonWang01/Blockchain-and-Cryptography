1.首先我們會在 ERC20\_token.sol 檔案，引入另一個 ERC20\_interface.sol 檔案。

```js
import "./ERC20_interface.sol"; //引入interface

contract ERC20_token is ERC20_interface { // 使用 is 繼承
   .....
}
```

2.再來我們會定義會用到的全域變數

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



