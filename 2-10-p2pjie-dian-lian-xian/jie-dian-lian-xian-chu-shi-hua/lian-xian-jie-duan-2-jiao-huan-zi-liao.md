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

2.Inv

接著另外一個節點收到getBlocks後，他會比較 getBlocks 傳過來的這些區塊和自己的區塊鏈上擁有的比較，比較之後，把另外一個節點還缺少的區塊，用Inv message的方式回傳回去，其類似於一份清單，上面告知另外一個節點他還少了哪些區塊還沒同步。



Inv還可用來回覆其他訊息，下圖為其種類圖

> ![](/assets/螢幕快照 2017-12-26 下午12.39.53.png)https://en.bitcoin.it/wiki/Protocol\_documentation\#Inventory\_Vectors



