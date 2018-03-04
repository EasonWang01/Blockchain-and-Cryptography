# 內建全域變數

#### 1.幣別單位

wei, finney, szabo, ether

可以直接使用，例如：

```js
if(msg.value != 1 ether) throw;
```

#### 2.時間單位

seconds, minutes, hours, days, weeks, years

範例：

```js
function f(uint start, uint daysAfter) public {
    if (now >= start + daysAfter * 1 days) {
      // ...
    }
}
```

#### 3.區塊鏈相關資訊

以下為預設存在之區塊鏈相關資訊的變數

```go
block.blockhash(uint blockNumber) returns (bytes32)
可以查詢最近256個區塊Hash

block.coinbase (address)
回傳挖到該區塊的礦工地址

block.difficulty (uint)
回傳區塊難度

block.gaslimit (uint)
回傳區塊 gaslimit

block.number (uint)
回傳當下區塊高度

block.timestamp (uint)
回傳當下區塊的timestamp

msg.data (bytes)
回傳完整的calldata，可參考：https://ethereum.stackexchange.com/questions/14037/what-is-msg-data

msg.gas (uint)
回傳剩餘的gas

msg.sender (address)
回傳呼叫合約的人之地址

msg.sig (bytes4)
回傳前四個calldata的 bytes 

msg.value (uint)
回傳接收到的金額，單位為wei

now (uint)
回傳當下區塊的 timestamp (類似於block.timestamp)

tx.gasprice (uint)
回傳交易之 gas price

tx.origin (address)
回傳原始發送交易的人之地址，不建議使用。假設用户從合約A調用合約B，此時合約A的tx.origin 和 msg.sender 都是用戶。
但是合約B :tx.origin 是用戶，但msg.sender為合約A.
```

> msg來源可以是合約或是用戶，如果一個合約被另一個合約調用則msg則會指向調用該合約的合約。

#### 錯誤處理

為了避免EVM在執行時發生錯誤後直接消耗掉所有提供的Gas，以及一些不可預料的情況導致合約產生Bug，我們會使用以下方式來處理條件內容。

```go
assert(bool condition):
如果情況錯誤則拋出錯誤，通常用於判斷內部邏輯錯誤。

require(bool condition):
如果情況錯誤則拋出錯誤，通常用於判斷使用者輸入之參數，或是外部合約回傳過來的值。

revert():
用來主動拋出錯誤，並回復狀態。
```

> assert\(\) 拋錯時會消耗使用的Gas，但 require\(\) 與 revert\(\) 會退還Gas。
>
> 可參考：[http://solidity.readthedocs.io/en/develop/control-structures.html?highlight=require\#error-handling-assert-require-revert-and-exceptions](http://solidity.readthedocs.io/en/develop/control-structures.html?highlight=require#error-handling-assert-require-revert-and-exceptions)

#### 數學與密碼學相關函式

```go
addmod(uint x, uint y, uint k) returns (uint):
計算 (x + y) % k 

mulmod(uint x, uint y, uint k) returns (uint):
計算 (x * y) % k

keccak256(...) returns (bytes32):
計算 Ethereum-SHA-3 (Keccak-256)之 hash，例如：keccak256(6382179)，keccak256(97, 98, 99)，逗號會自動串接

sha256(...) returns (bytes32):
計算 SHA-256 hash ，例如：sha256("abc")，sha256(11, 98, 99)，逗號會自動串接

sha3(...) returns (bytes32):
類似於 keccak256

ripemd160(...) returns (bytes20):
計算 RIPEMD-160 hash

ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address):
從簽章復原回adddress
// 可參考：https://ethereum.stackexchange.com/questions/1777/workflow-on-signing-a-string-with-private-key-followed-by-signature-verificatio
```

#### 合約相關

```go
this (current contract’s type):
指向當前合約，例如: this.balance

selfdestruct(address recipient):
把合約永久從區塊鏈上移除，並且把合約上的金額轉移給參數的地址

suicide(address recipient):
類似於selfdestruct，但selfdestruct為新版本才有，至於改名字原因可參考：
// https://github.com/ethereum/EIPs/blob/master/EIPS/eip-6.md
```



