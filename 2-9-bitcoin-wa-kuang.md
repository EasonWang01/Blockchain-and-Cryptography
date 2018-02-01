## Bitcoin 挖礦

我們常在新聞或文章上看到許多人使用了CPU、顯示卡或是專門的礦機進行挖礦，並且加入了礦池，那麼究竟什麼是挖礦呢？

挖礦其實就是在幫忙驗證區塊鏈中的交易，因為區塊鏈是去中心化的，所以必須想一個辦法讓發送出來的交易是合理的並可以被驗證，這時這些挖礦的礦工就提供了算力，來進行交易的驗證，並算出新的區塊。

每個礦工會把目前尚未被驗證的交易蒐集起來，然後納入自己目前正在計算的區塊中，如果這位礦工成功的算出了新區塊，則他所納入該區塊的交易也都會被驗證，交易被驗證的意思及代表著交易被納入到鏈上新的區塊中。

#### 挖礦獎勵

礦工挖礦獲得的獎勵有兩種，區塊獎勵與新區塊內所含的所有交易之手續費。

區塊獎勵每隔210000個區塊後會減少一半，2009 年一月每個區塊獎勵為 50 個比特幣，到 2012 年 11 月減半為 25 個，之後大致是四年減少一半，而全部 20,999,999,980 個比特幣都被挖完的時間約為2140年，之後挖礦的獎勵只會剩下交易手續費。

#### 區塊難度

比特幣中平均每十分鐘會挖出一個新區塊，而為了控制新區塊產生的時間，比特幣有公式來控制其挖礦難度。

> 難度會記錄在區塊頭中，類似如下:
>
> ```
> Difficulty: 1,873,105,475,221.61
> ```

難度於每 2016 個區塊被挖出後會自動按照公式更改一次。

1.使用Bitcoin API `getDifficulty`取得現在的難度

> [https://blockexplorer.com/api/status?q=getDifficulty](https://blockexplorer.com/api/status?q=getDifficulty)

2.從難度去反推現在要計算的Target \(利用公式\)

```
target = coefficient * 2 ** (8 * (exponent – 3))
```

假設在區塊277,316中，它的難度值為 0x1903a30c

```
將0x1903a30c分為前兩位十六進位數字，與後面的六位。在這個區塊裡，0x19為前兩位，而 0x03a30c 為後六位。

由此上面公式及難度的值 0x1903a30c，可得：
target = 0x03a30c * 2 ** (0x08 * (0x19 - 0x03))

= 0x03a30c * 2 ** (0x08 * 0x16)

= 0x03a30c * 2 ** 0xB0

按十進位計算為：
target = 238348 * (2 ** 176) = 22,829,202,948,393,929,850,749,706,076,701,368,331,072,452,018,388,575,715,328

轉為十六進制後為：
target =0x0000000000000003A30C00000000000000000000000000000000000000000000
(前面補0，讓 Target 為 256 bits)
```

> 比特幣的最高的目標值，也是最簡單的難度 \( difficulty 1 \) 為以下，用其計算的難度稱為 \( bdiff \)：
>
> ```
> 0x00ffff * 2 ** (8 * (0x1d - 3)) = 0x00000000ffff0000000000000000000000000000000000000000000000000000
> ```
>
> > 在礦機中通常會將以上的結果以下列表示，用其計算的難度稱為 Pool Difficulty （ pdiff ）：
> >
> > ```
> > 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
> > ```
>
> 而目前難度計算方式為：目標最高值 / 當前目標值
>
> ```
> difficulty = difficulty_1_target(最簡單的難度) / current_target
> ```

[https://en.bitcoin.it/wiki/Difficulty](https://en.bitcoin.it/wiki/Difficulty)

#### 挖礦的競爭方式

挖出新區塊可以獲得比特幣獎勵，那麼礦工們是如何互相競爭取得獎勵呢？

礦工們會把區塊頭進行兩次 SHA-256 的加密，只要比一個 Target 數小，及可挖到新的區塊。

區塊頭結構類似如下，包含在區塊中\(以下僅為範例，數值在每個區塊都會不同\)

```
Block version: 02000000  
Previous Hash: b6ff0b1b1680a2862a30ca44d346d9e8910d334beb48ca0c00000000000000009d10aa52ee949386ca9385695f04ede2
Merkle root:   70dda20810decd12bc9b048aaab31471
Unix time:     24d95a54
Target:        30c31b18
Nonce:         fe9f0864
```

以下為挖礦的概念範例 :

```js
const crypto = require('crypto');

const target = "00010000a2230000000000000000000000000000000000000000000000000";
const startTime = Date.now();
function crypto256(input) {
  return crypto.createHash('sha256')
    .update(input)
    .digest('hex')
}

const double_sha256 = (header) => crypto256(crypto256(header));


let nonce = 0;
while (1) {
  nonce += 1;
  const header = {
    version: Buffer.from("0x02000000", 'hex'),
    nonce,
    previousHash: Buffer.from("dd0e2b79d79be0dfca96b4ad9ac85600097506f06f52bb74f769e02fcc66dec6", 'hex'),
    merkleRoot: Buffer.from("c91c008c26e50763e9f548bb8b2fc323735f73577effbc55502c51eb4cc7cf2e", 'hex'),
    bits: Buffer.from("437129626", 'hex'),
    TimeStamp: Date.now()
  };
  let cal = double_sha256(JSON.stringify(header));
  console.log(cal)
  if (cal < target) {
    console.log("\nSuccess Hash: " + cal);
    console.log("Number of calculations: " + nonce + ' times')
    console.log(`Time consumed: ${(Date.now() - startTime) / 1000} second`)
    break;
  }
}
```

執行後會進行運算，成功後會產生如下訊息：

![](/assets/螢幕快照 2018-02-01 下午8.55.32.png)

