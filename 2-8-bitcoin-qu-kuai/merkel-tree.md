# Merkle Tree

由一位[Ralph Merkle](https://en.wikipedia.org/wiki/Ralph_Merkle) 的外國人在1979年發佈，主要可以用來做訊息的快速驗證，可以想像是有一棵樹，樹上有許多葉子，每個葉子都有自己的ID，今天如果要驗證這棵樹的所有葉子是否和昨天是相同的，可以把所有葉子的ID都兩兩相加做雜湊，之後再繼續把雜湊跟其隔壁葉子的兩兩雜湊結果繼續做雜湊，直到產生最後一個 Root 雜湊，再把這兩天算出來的 Root 雜湊做比較即可。

可參考如下介紹:

[https://en.wikipedia.org/wiki/Merkle\_tree](https://en.wikipedia.org/wiki/Merkle_tree)

[https://en.wikipedia.org/wiki/Merkle\_signature\_scheme](https://en.wikipedia.org/wiki/Merkle_signature_scheme)

比特幣使用了Merkle Tree的結構，把區塊中的所有交易，兩兩做SHA256的Hash，最後產生一個Merkle Root，類似如下圖

![](/assets/Hash_Tree.svg.png)在比特幣中，會把區塊中每一筆交易的TXid用兩次sha256做加密

```
HA =  SHA256(SHA256(交易A))
```

```
HB =  SHA256(SHA256(交易B))
```

然後再把兩個交易的雜湊字串連結再一起，之後再繼續做一樣的加密，直到出現Merkle根為止。

範例：

```js
var crypto = require('crypto');

const tx1 = '51b78168d94ec307e2855697209275d477e05d8647caf29cb9e38fb6a4661145';
const tx2 = 'dasd94ec307e2855697209275d477e05d8647caf29cb9e38fb6a4661145ddddd';

const tx3 = 'b45ff1f1aa88de71005fd14487328cbc74bc47fd783aa6734c1e4c7950962cc4';
const tx4 = 'caede6064e49b54ae53ac44fadd01e66be907f1bbd1daedd8b3f3f9561447f4f';

function crypto256(input) {
    var final = crypto.createHash('sha256')
        .update(input)
        .digest('hex');
    return final
}

var hash1 = crypto256(crypto256(tx1));
var hash2 = crypto256(crypto256(tx2));

var hash3 = crypto256(crypto256(tx3));
var hash4 = crypto256(crypto256(tx4));


var hash1_hash2 = crypto256(crypto256(hash1 + hash2));
var hash3_hash4 = crypto256(crypto256(hash3 + hash4));

var root = crypto256(crypto256(hash1_hash2 + hash3_hash4));

console.log('Merkle Root為:' + root);
```

> 每一筆交易都會被放在樹的最下層葉子處，如果最後為奇數個葉子，則最後一個交易會被複製一次成為一個葉子，使其樹具有偶數個葉子，方便兩兩做Hash

Bitcoin Merkle Tree原始碼:

> [https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/consensus/merkle.cpp](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/consensus/merkle.cpp)



