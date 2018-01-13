#### 1.幣別單位

wei, finney, szabo , ether

可以直接使用，例如以下：

```
if(msg.value != 1 ether) throw;
```

#### 2.時間單位

seconds, minutes, hours, days, weeks , years

範例：

```
function f(uint start, uint daysAfter) public {
    if (now >= start + daysAfter * 1 days) {
      // ...
    }
}
```

#### 3.區塊鏈相關資訊

以下為預設存在之區塊鏈相關資訊的變數

```
block.blockhash(uint blockNumber) returns (bytes32):
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

```
assert(bool condition):
如果情況錯誤則拋出錯誤，通常用於判斷內部邏輯錯誤。

require(bool condition):
如果情況錯誤則拋出錯誤，通常用於判斷使用者輸入之參數，或是外部合約回傳過來的值。

revert():
用來主動拋出錯誤，並回復狀態。
```

> assert\(\)拋錯時會消耗使用的Gas，但require\(\)與revert\(\)會退還Gas。
>
> 可參考：[http://solidity.readthedocs.io/en/develop/control-structures.html?highlight=require\#error-handling-assert-require-revert-and-exceptions](http://solidity.readthedocs.io/en/develop/control-structures.html?highlight=require#error-handling-assert-require-revert-and-exceptions)



