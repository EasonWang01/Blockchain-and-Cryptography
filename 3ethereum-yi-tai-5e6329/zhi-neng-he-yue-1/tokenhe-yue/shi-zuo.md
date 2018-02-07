# 實作

#### 啟動本地節點

我們先啟動節點

```
geth --dev --rpc --rpcport 8545 --datadir ./Ethtest_ERC20 --rpccorsdomain="*" --rpcapi="eth,net,web3,personal" console
```

> 因為之後使用的Mist錢包需要具有相關RPC API 權限，所以加上：--rpcapi="eth,net,web3,personal"

這時裡面會有一個預設account，而我們再新增兩個帳號

> 輸入兩次以下指令：

```
personal.newAccount()
```

![](/assets/螢幕快照 2018-01-16 下午8.17.40.png)

> 因為--dev會預先分配Ether給第一個帳號，這時我們轉帳一些Ether給第二個帳號，方便後面執行合約function消耗Gas時使用。

轉帳 \( 從帳號0到帳號1 \)

```
eth.sendTransaction({from: eth.accounts[0], to: eth.accounts[1], value: web3.toWei(10, "ether")})
```

查詢餘額 \( 帳號1 \)

```
web3.fromWei(eth.getBalance(eth.accounts[1]))
```

#### ![](/assets/螢幕快照 2018-01-16 下午8.18.45.png)

#### 打開Remix IDE

[http://remix.ethereum.org](http://remix.ethereum.org)

> 一樣於網址輸入http開頭 \( 不使用https \) 的Remix IDE 網址，之後才能連線到本地節點

接著跟先前章節一樣，與本地節點連線。

![](/assets/kasd.png)

#### 新增合約

此時我們點選如下圖的加號按鈕，新增兩個檔案

```
ERC20_interface.sol
ERC20_token.sol
```

![](/assets/90asd.png)

我們先開啟ERC20\_interface.sol檔案，並加入如下程式碼 :

ERC20\_interface.sol

```js
pragma solidity ^0.4.19;

contract ERC20_interface {
    uint256 public totalSupply;

    function balanceOf(address _owner) public view returns (uint256 balance);

    function transfer(address _to, uint256 _value) public returns (bool success);

    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success);

    function approve(address _spender, uint256 _value) public returns (bool success);

    function allowance(address _owner, address _spender) public view returns (uint256 remaining);

    event Transfer(address indexed _from, address indexed _to, uint256 _value); 
    event Approval(address indexed _owner, address indexed _spender, uint256 _value);
}
```

> 此即為在ERC20所定義之需要實作的介面。

然後開啟另一個ERC20\_token.sol檔案，新增如下程式碼 :

> 合約說明均寫在註解內

```js
pragma solidity ^0.4.19;      // 指定Compiler版本

import "./ERC20_interface.sol";  //引入interface


contract ERC20_token is ERC20_interface {   // 使用 is 繼承

    uint256 constant private MAX_UINT256 = 2**256 - 1; //避免超過產生overflow
    mapping (address => uint256) public balances;   // 查詢特定地址的餘額
    mapping (address => mapping (address => uint256)) public allowed;  // 查詢特定地址可以給另一個地址的轉帳配額

    string public name;             // 幫合約取名稱
    uint8  public decimals = 18;    // 小數點，官方建議為18
    string public symbol;           // e.g. ^_^
    address owner;
    uint256 public buyPrice;   // 一單位Ether可以換多少token
    uint private weiToEther = 10 ** 18; // 把單位從wei轉為Ether

    // 建構子，一開始即會執行，需要提供總量、價格、名稱、標誌
    function ERC20_token(
        uint256 _initialSupply,
        uint256 _buyPrice,
        string _tokenName,
        string _tokenSymbol
    ) public {
        totalSupply = _initialSupply * 10 ** uint256(decimals); // token總量
        balances[msg.sender] = totalSupply;                    // 設定合約token擁有者為合約部屬者      

        name = _tokenName;                                   // token名稱
        symbol = _tokenSymbol;                               // token 標誌
        owner = msg.sender;                                  // 合約擁有人
        buyPrice = _buyPrice;                                // 每單位 ether 之價格
    }

    // 限定只有合約部屬人才能執行特定function
    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    // 查詢餘額
    function balanceOf(address _owner) public view returns (uint256 balance) {
      return balances[_owner];
    }

    // 從合約擁有人地址轉帳
    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(balances[msg.sender] >= _value);
        balances[msg.sender] -= _value;
        balances[_to] += _value;
        Transfer(msg.sender, _to, _value);
        return true;
    }

    // 從某一人地址轉給另一人地址，需要其轉帳配額有被同意，可想像為小明(msg.sender)用爸爸的副卡(_from)轉帳給別人(_to)
    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
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

    // 給予特定帳號轉帳配額  類似小明的爸爸(msg.sender)給小明(_spender)一張信用卡副卡，額度為value
    function approve(address _spender, uint256 _value) public returns (bool success) {
        allowed[msg.sender][_spender] = _value;
        Approval(msg.sender, _spender, _value);
        return true;
    }

    // 查詢特定帳號轉給另一帳號之轉帳配額
    function allowance(address _owner, address _spender) public view returns (uint256 remaining) {
        return allowed[_owner][_spender];
    }   

    // 設定token購買價格，只有合約部屬者可以設定
    function setPrice(uint _price) public onlyOwner {
        buyPrice = _price;
    }

    // 購買token
    function buy() public payable {
        uint amount;
        amount = (msg.value / weiToEther) * buyPrice * 10 ** uint256(decimals);    // 購買多少token
        require(balances[owner] >= amount);              // 檢查還有沒有足夠token可以賣
        balances[msg.sender] += amount;                  // 增加購買者token   
        balances[owner] -= amount;                        // 減少擁有者token
        Transfer(owner, msg.sender, amount);               // 產生token轉帳log
    }

    // 從合約轉出Ether到部屬者帳戶
    function withdraw(uint amount) public onlyOwner {
        owner.transfer(amount * weiToEther);
    }

    // 從區塊鏈上移出合約
    function deleteContract() public onlyOwner {
        selfdestruct(owner);  // 將合約剩餘的Ether轉給owner
    }
}
```



