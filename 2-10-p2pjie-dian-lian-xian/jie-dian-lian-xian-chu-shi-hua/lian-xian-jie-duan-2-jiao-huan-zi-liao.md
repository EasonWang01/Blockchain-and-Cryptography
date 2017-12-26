## 1. getBlocks

在建立連線後就要開始交換資料了，這時節點會向其他節點發出 getblocks 請求，這個節點會將他目前為止擁有的所有區塊header hash發送出去，告訴其他的節點，自己目前的區塊鏈同步到什麼程度了。

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

## 2.Inv \(inventory\)

接著另外一個節點收到getBlocks後，他會比較 getBlocks 傳過來的這些區塊和自己的區塊鏈上擁有的比較，比較之後，把另外一個節點還缺少的區塊，用Inv message的方式回傳回去，其類似於一份清單，上面告知另外一個節點他還少了哪些區塊還沒同步。

Inv還可用來回覆其他訊息，下圖為其種類圖

> ![](/assets/螢幕快照 2017-12-26 下午12.39.53.png)[https://en.bitcoin.it/wiki/Protocol\_documentation\#Inventory\_Vectors](https://en.bitcoin.it/wiki/Protocol_documentation#Inventory_Vectors)

![](/assets/螢幕快照 2017-12-26 下午12.53.22.png)

範例:

```js
const magicNum = "f9beb4d9";
const command = "696e76000000000000000000"
const payload_length = "25000000"
const checksum = "2b7991bd"

const payload = "010200000000000000000000000043a63945c421fb5de8c18a5d1e34304d3b10f222537f5b";
```

## 3. GetData

在拿到 Inv 清單後，節點會開始向另一個節點請求自己來沒有同步的區塊資料，而GetData請求的資料可以是[`tx`message](https://bitcoin.org/en/developer-reference#tx) 、

[`block`message](https://bitcoin.org/en/developer-reference#block) 、[`merkleblock`message](https://bitcoin.org/en/developer-reference#merkleblock) 、[`notfound`message](https://bitcoin.org/en/developer-reference#notfound) 等等。

![](/assets/螢幕快照 2017-12-26 下午1.07.33.png)

範例:

```js
const magicNum = "f9beb4d9";
const command = "676574646174610000000000"
const payload_length = "25000000"
const checksum = "2b7991bd"

const payload = "010200000000000000000000000043a63945c421fb5de8c18a5d1e34304d3b10f222537f5b";
```



