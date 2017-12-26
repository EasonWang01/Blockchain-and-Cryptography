在建立連線後就要開始交換資料了，這時節點會向其他節點發出 getblocks 請求，這個節點會將他目前為止擁有的所有區塊header hash發送出去，告訴其他的節點，自己目前的區塊鏈同步到什麼程度了。

1. getBlocks

![](/assets/螢幕快照 2017-12-26 下午12.21.31.png)

範例:

```js
const magicNum = "f9beb4d9";
const command = "676574626c6f636b73000000"
const payload_length = "65000000"
const checksum = "01a48340"

const payload = "7f11010002d39f608a7775b537729884d4e6633bb2105e55a16a14d31b00000000000000005c3e6403d40837110a2e8afb602b1c01714bda7ce23bea0a00000000000000000000000000000000000000000000000000000000000000000000000000000000";

const buffer = new Buffer(magicNum + command + payload_length + checksum + payload, 'hex');
```



