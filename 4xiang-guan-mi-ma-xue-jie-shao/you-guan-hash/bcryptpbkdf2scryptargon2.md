有一些函式是專門設計用來將密碼做雜湊的函式，這些即為本章我們所要介紹到的。

# Bcrypt

基於[Blowfish](https://baike.baidu.com/item/Blowfish/1677776)的一個加密算法，於1999年發表。

hash的過程會加入一個隨機的salt，然後salt跟password一起hash。但每次產生的salt會不一樣，所以同一個密碼每次進行產生的Hash會不同。

而Bcrypt還包含Round，越多Round會需要越多的計算時間。

再來你可能會想既然每次產生的Hash不同，那要怎麼進行訊息驗證呢？

解答是：在要驗證訊息时，會從原先的hash中取出salt \( 通常為Hash前面幾個字 \)，然後把取出來的salt跟輸入的password進行hash，最後將得到的结果跟保存在DB中的hash進行比對。

我們可用Node.js的Bcrypt模組：[https://github.com/kelektiv/node.bcrypt.js](https://github.com/kelektiv/node.bcrypt.js)

1.安裝

```
npm install bcrypt
```

2.生成Hash

```js
var bcrypt = require('bcrypt');
const saltRounds = 10;
const myPlaintextPassword = 'I_am_password';

bcrypt.genSalt(saltRounds, function (err, salt) {
  bcrypt.hash(myPlaintextPassword, salt, function (err, hash) {
    if (err) console.log(err);
    console.log(hash);
  });
});
```

3.驗證

```js
var bcrypt = require('bcrypt');
const myPlaintextPassword = 'I_am_password';

bcrypt.compare(myPlaintextPassword, "$2a$10$8QT.28zoo.jyFB2yvDURL.IM6gL4YJHGsr1PUysnFuGeqeDeooxuK", function(err, res) {
   console.log(res)
});

// compare填入要比對的密碼與剛才產生出的Hash
```

# PBKDF2

全名為Password-Based Key Derivation Function，利用HMAC的方式來加入password和salt然後一樣進行多次的重複計算。

Node.js原生即提供了PBKDF2算法。

```js
const crypto = require('crypto');
crypto.pbkdf2('secret', 'salt', 100000, 64, 'sha512', (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // '3745e48...08d59ae'
});
```

> 其中參數依序為
>
> password 要進行Hash的字串
>
> salt 加入的隨機值
>
> iterations 計算次數
>
> keylen 產生的Hash長度\(bytes\)
>
> digest 使用的Hash算法 e.g. SHA-512

# Scrypt

此Hash方法加入了需要大量記憶體運算的設計，作法為利用大量記憶體，並將運算資料儲存在記憶體內供演算法計算，如此可避免一些客製化的硬體快速的計算出Hash。而其也是Litecoin與Dogecoin所使用的Hash演算法。

