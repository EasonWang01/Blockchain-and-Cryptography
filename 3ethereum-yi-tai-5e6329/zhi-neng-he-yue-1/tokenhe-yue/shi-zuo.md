#### 啟動本地節點

我們先啟動節點

```
geth --dev --rpc --rpcport 8545 --datadir ./Ethtest_ERC20 --rpccorsdomain="*"  console
```

這時裡面會有一個預設account，我們再新增兩個帳號

```
personal.newAccount()
// 輸入兩次
```

> 因為--dev會預先分配Ether給第一個帳號，這時我們轉帳一些Ether給第二個帳號，方便後面執行合約function消耗Gas時使用。

轉帳 \( 從帳號0到帳號1 \)

```
eth.sendTransaction({from: eth.accounts[0], to: eth.accounts[1], value: web3.toWei(10, "ether")})
```

查詢餘額 \( 帳號1 \)

```
web3.fromWei(eth.getBalance(eth.accounts[1]))
```

#### 打開Remix IDE

[http://remix.ethereum.org](http://remix.ethereum.org)

> 一樣使用http protocol，才能連線到本地節點

接著跟先前章節一樣，與本地節點連線。

![](/assets/kasd.png)

#### 新增合約

此時我們新增兩個檔案

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

```js
pragma solidity ^0.4.19;      // 指定Compiler版本

import "./ERC20_interface.sol";  //引入interface


contract ERC20_token is ERC20_interface {   // 使用 is 繼承

    uint256 constant private MAX_UINT256 = 2**256 - 1; //避免超過產生overflow
    mapping (address => uint256) public balances;   // 查詢特定地址的餘額
    mapping (address => mapping (address => uint256)) public allowed;  // 查詢特定地址可以給另一個地址的轉帳配額

    string public name;        // 幫合約取名稱
    uint8  public decimals;    // 小數點
    string public symbol;      // e.g. CTX

    // 建構子，一開始即會執行
    function ERC20_token(
        uint256 _initialAmount,
        string _tokenName,
        uint8 _decimalUnits,
        string _tokenSymbol
    ) public {
        balances[msg.sender] = _initialAmount;               // token總量
        totalSupply = _initialAmount;                        // 
        name = _tokenName;                                   // token名稱
        decimals = _decimalUnits;                            // 小數點
        symbol = _tokenSymbol;                               // token 標誌
    }

    // 從合約擁有人地址轉帳
    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(balances[msg.sender] >= _value);
        balances[msg.sender] -= _value;
        balances[_to] += _value;
        Transfer(msg.sender, _to, _value);
        return true;
    }
    
    // 從某一人地址轉給另一人地址，需要其轉帳配額有被同意
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
    // 查詢餘額
    function balanceOf(address _owner) public view returns (uint256 balance) {
        return balances[_owner];
    }
    
    // 給予特定帳號轉帳配額
    function approve(address _spender, uint256 _value) public returns (bool success) {
        allowed[msg.sender][_spender] = _value;
        Approval(msg.sender, _spender, _value);
        return true;
    }

    // 查詢特定帳號轉給另一帳號之轉帳配額
    function allowance(address _owner, address _spender) public view returns (uint256 remaining) {
        return allowed[_owner][_spender];
    }   
}
```



