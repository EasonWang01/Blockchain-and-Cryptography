## 1. GetBlocks

在建立連線後就要開始交換資料了，這時節點會向其他節點發出 getblocks 請求，這個節點會將他目前為止擁有的所有區塊header hash發送出去，告訴其他的節點，自己目前的區塊鏈同步到什麼程度了。

![](/assets/螢幕快照 2017-12-26 下午12.21.31.png)將上面程式的 Buffer 部分改為如下再次執行，即可模擬 :

```js
const magicNum = "f9beb4d9";
const command = "676574626c6f636b73000000" // getblocks
const payload_length = "65000000"
const checksum = "01a48340"

const payload = "7f11010002d39f608a7775b537729884d4e6633bb2105e55a16a14d31b00000000000000005c3e6403d40837110a2e8afb602b1c01714bda7ce23bea0a00000000000000000000000000000000000000000000000000000000000000000000000000000000";

const buffer = new Buffer(magicNum + command + payload_length + checksum + payload, 'hex');
```

## 2.Inv \(inventory\)

接著另外一個節點收到getBlocks後，他會比較 getBlocks 傳過來的這些區塊和自己的區塊鏈上擁有的比較，比較之後，把另外一個節點還缺少的區塊，用Inv message的方式回傳回去，類似於一份清單，上面告知另外一個節點他還少了哪些區塊還沒同步。

Inv還可用來回覆其他訊息，下圖為其他可用類型

![](/assets/螢幕快照 2017-12-26 下午12.39.53.png)[https://en.bitcoin.it/wiki/Protocol\_documentation\#Inventory\_Vectors](https://en.bitcoin.it/wiki/Protocol_documentation#Inventory_Vectors)

![](/assets/螢幕快照 2017-12-26 下午12.53.22.png)將上面程式的 Buffer 部分改為如下再次執行，即可模擬：

```js
const magicNum = "f9beb4d9";
const command = "696e76000000000000000000" // inv
const payload_length = "25000000"
const checksum = "2b7991bd"

const payload = "010200000000000000000000000043a63945c421fb5de8c18a5d1e34304d3b10f222537f5b";

const buffer = new Buffer(magicNum + command + payload_length + checksum + payload, 'hex');
```

## 3. GetData

在拿到 Inv 清單後，節點會開始向另一個節點請求自己還沒有同步的區塊資料，而GetData請求的資料可以是[`tx`message](https://bitcoin.org/en/developer-reference#tx) 、

[`block`message](https://bitcoin.org/en/developer-reference#block) 、[`merkleblock`message](https://bitcoin.org/en/developer-reference#merkleblock) 、[`notfound`message](https://bitcoin.org/en/developer-reference#notfound) 等等。

![](/assets/螢幕快照 2017-12-26 下午1.07.33.png)將上面程式的 Buffer 部分改為如下再次執行，即可模擬：

```js
const magicNum = "f9beb4d9";
const command = "676574646174610000000000" // getdata
const payload_length = "25000000"
const checksum = "2b7991bd"

const payload = "010200000000000000000000000043a63945c421fb5de8c18a5d1e34304d3b10f222537f5b";

const buffer = new Buffer(magicNum + command + payload_length + checksum + payload, 'hex');
```

## 4. Block

收到GetData的請求後，另外一個節點就會回應，將被請求的區塊傳送給請求的節點。

礦工挖到新的區塊後也會以此方式來廣播新區塊。

![](/assets/螢幕快照 2017-12-26 下午3.03.55.png)將上面程式的 Buffer 部分改為如下再次執行，即可模擬 :

```js
const magicNum = "f9beb4d9";
const command = "626c6f636b00000000000000" // block
const payload_length = "d8000000"
const checksum = "b22f22d4"

const payload = "01000000c294d21ace17d30fece740407c87a98128064ba358ceab825f5bb6c200000000419fdf9f62c5054733b7ada16dc1ed8047b46bac560b2f68662bec696f2f5dfdd540a749ffff001d2a5f0ac00101000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0804ffff001d02b309ffffffff0100f2052a010000004341047a002f75cbe384fdaa65885873da469a178d46fb27991f5451c9bd3accb49a9bff1cfadb9150ee7ed81c66ca822f3ec8c60d9eb4ca16cff9fb97132a71fd70faac00000000";

const buffer = new Buffer(magicNum + command + payload_length + checksum + payload, 'hex');
```



