# SHA256實作原理



### 第一步:

```
1.把明文轉為二進位的數字bits
2.在最後面先放一個數字1
3.在最後面補 k 個 0 直到明文bits的長度 + 1 + k % 512 = 448
```

然後最後面再加上64bits的數字，內容為明文的長度\(以二進位表示\) 例如：abc之ASCII二進位長度為24bits

24之二進位為11000

因為不足64bit於是在前面補0使其成為：00....11000

\(以ABC為例子\)

![](/assets/52542.png)![](/assets/981a7fcf-8be1-44d0-966d-f66bf0c8ab18.png)

### 第二步:

寫出八個initial hash value\(此為被定義好之固定值\)

![](/assets/755.png)![](/assets/844c13d2-4afc-4201-9367-7521aea3cdab.png)

```
原理: 
Python:  
hex(int(math.modf(math.sqrt(2))[0]*(1<<32)))
     
Javascript: 
先取Math.sqrt(2)的小數，然後乘上2 ** 32，之後轉為16進位，然後取小數前面部分  
範例: ((0.4142135623730951 * (2**32)).toString(16)).substring(0,8)


H(0)
1 = 6a09e667            


H(0)
2 = bb67ae85
先進入到python命令列，然後輸入import math
hex(int(math.modf(math.sqrt(3))[0]*(1<<32)))

H(0)
3 = 3c6ef372
hex(int(math.modf(math.sqrt(5))[0]*(1<<32)))

H(0)
4 = a54ff53a
hex(int(math.modf(math.sqrt(7))[0]*(1<<32)))

H(0)
5 = 510e527f
hex(int(math.modf(math.sqrt(11))[0]*(1<<32)))

H(0)
6 = 9b05688c
hex(int(math.modf(math.sqrt(13))[0]*(1<<32)))

H(0)
7 = 1f83d9ab
hex(int(math.modf(math.sqrt(17))[0]*(1<<32)))

H(0)
8 = 5be0cd19
hex(int(math.modf(math.sqrt(19))[0]*(1<<32)))


之後可以發現上面八個initial number中math.sqrt(x) x為最小的八個質數
```

> 附註:1&lt;&lt;32在python和js行為會不同，可參考[https://stackoverflow.com/questions/45024682/different-behavior-for-1-32-compare-javascript-to-python](https://stackoverflow.com/questions/45024682/different-behavior-for-1-32-compare-javascript-to-python)

# \#第三步

64個固定k值

![](/assets/4587.png)![](/assets/99a8531e-ec2c-4dd3-969d-5eb3198422b2.png)

```
原理: 用math cube root的方法找首64個質數

以下用Python為例:

(先定義cube方法，意思即為開三次方根，js可直接用Math.cbrt())
def cube(x):
    if 0<=x: return x**(1./3.)
    return -(-x)**(1./3.)


-------------------------------------------------------------    

 hex(int(math.modf(cube(2))[0]*(1<<32)))       //0x428a2f98

 hex(int(math.modf(cube(3))[0]*(1<<32)))       //0x71374491

 ...
```

# \#第四步:

使用sha256定義的六個hash function

![](/assets/74.png)![](/assets/7a78355a-4918-4eca-bfe3-b1d69e5a4420.png)

其中符號定義如下

![](/assets/7857.png)![](/assets/29de9148-4747-4151-9e99-b84cbf5603d6 %281%29.png)

寫成程式為:

```js
function ch (x, y, z) {
  return z ^ (x & (y ^ z))
}

function maj (x, y, z) {
  return (x & y) | (z & (x | y))
}

function sigma0 (x) {
  return (x >>> 2 | x << 30) ^ (x >>> 13 | x << 19) ^ (x >>> 22 | x << 10)
}

function sigma1 (x) {
  return (x >>> 6 | x << 26) ^ (x >>> 11 | x << 21) ^ (x >>> 25 | x << 7)
}

function gamma0 (x) {
  return (x >>> 7 | x << 25) ^ (x >>> 18 | x << 14) ^ (x >>> 3)
}

function gamma1 (x) {
  return (x >>> 17 | x << 15) ^ (x >>> 19 | x << 13) ^ (x >>> 10)
}
```

# \#完整範例

```js
const util = require('util');
var bigInt = require("big-integer");


function Hash (blockSize, finalSize) {
  this._block = new Buffer(blockSize)
  this._finalSize = finalSize
  this._blockSize = blockSize
  this._len = 0
}

Hash.prototype.digest = function (data, enc) {

data = (data).toString(); 
//////////////////
let s = ""
let s1 = ""

//將字串轉為二進位ASCII
function ascii (a) { 
  let dd = "";
  for(let i = 0 ; i< a.length; i++) {
    let charAscII = a.charAt(i).charCodeAt(0).toString(2)
    while(charAscII.length < 8) { //最前面的0都會被省略，所以手動加上
      charAscII = 0 + charAscII
    }
    dd += charAscII; 
  }
  return dd
}

let msgLengthToBinary = ((ascii(data).length).toString(2).length); //計算訊息有幾個字元再轉為二進位數字的長度
let BinarymsgLength = (ascii(data).length); //訊息的二進位的長度

for(i = 0; i < 448 - 1 - BinarymsgLength; i++) {s += 0} //前面補448bit 0
for(i = 0; i < 64 - msgLengthToBinary ; i++) {s1 += 0} //後面補64 bit 0

let msgLength = ascii(data).length;
let frontPad = ascii(data) + 1 + s + s1 + (ascii(data).length).toString(2);


let c0 = bigInt(bin2dec(frontPad)).toString(16);  //涉及大數轉進位(MAX_SAFE_INTEGER)


let initPadedValue = c0.substring(0, c0.length - 2) + msgLength.toString(16); //因為太大的數parseInt並轉二進位後面的數字會被省略掉，所以把它加上


let initPadedValueBuffer = Buffer.from(initPadedValue, 'hex');

  this._update(initPadedValueBuffer) //傳入一開始pad好之buffer進行main loop hash function
  var hash = this._hash() //拼接main loop好的a-h八個區塊

  return enc ? hash.toString(enc) : hash  //轉為16進位
}


function bin2dec(str){ //大數二進位在用parseInt轉十進位時後面常被省略，所以用此方法
    var dec = str.toString().split(''), sum = [], hex = [], i, s
    while(dec.length){
        s = 1 * dec.shift()
        for(i = 0; s || i < sum.length; i++){
            s += (sum[i] || 0) * 2
            sum[i] = s % 10
            s = (s - sum[i]) / 10
        }
    }
    while(sum.length){
        hex.push(sum.pop().toString(10))
    }
    return hex.join('')
}


var K = [
  0x428A2F98, 0x71374491, 0xB5C0FBCF, 0xE9B5DBA5,
  0x3956C25B, 0x59F111F1, 0x923F82A4, 0xAB1C5ED5,
  0xD807AA98, 0x12835B01, 0x243185BE, 0x550C7DC3,
  0x72BE5D74, 0x80DEB1FE, 0x9BDC06A7, 0xC19BF174,
  0xE49B69C1, 0xEFBE4786, 0x0FC19DC6, 0x240CA1CC,
  0x2DE92C6F, 0x4A7484AA, 0x5CB0A9DC, 0x76F988DA,
  0x983E5152, 0xA831C66D, 0xB00327C8, 0xBF597FC7,
  0xC6E00BF3, 0xD5A79147, 0x06CA6351, 0x14292967,
  0x27B70A85, 0x2E1B2138, 0x4D2C6DFC, 0x53380D13,
  0x650A7354, 0x766A0ABB, 0x81C2C92E, 0x92722C85,
  0xA2BFE8A1, 0xA81A664B, 0xC24B8B70, 0xC76C51A3,
  0xD192E819, 0xD6990624, 0xF40E3585, 0x106AA070,
  0x19A4C116, 0x1E376C08, 0x2748774C, 0x34B0BCB5,
  0x391C0CB3, 0x4ED8AA4A, 0x5B9CCA4F, 0x682E6FF3,
  0x748F82EE, 0x78A5636F, 0x84C87814, 0x8CC70208,
  0x90BEFFFA, 0xA4506CEB, 0xBEF9A3F7, 0xC67178F2
]

var W = new Array(64)

function Sha256 () {
  this.init()

  this._w = W // new Array(64)

  Hash.call(this, 64, 56)
}

util.inherits(Sha256, Hash)

Sha256.prototype.init = function () {
  this._a = 0x6a09e667
  this._b = 0xbb67ae85
  this._c = 0x3c6ef372
  this._d = 0xa54ff53a
  this._e = 0x510e527f
  this._f = 0x9b05688c
  this._g = 0x1f83d9ab
  this._h = 0x5be0cd19

  return this
}

function ch (x, y, z) {
  return z ^ (x & (y ^ z))
}

function maj (x, y, z) {
  return (x & y) | (z & (x | y))
}

function sigma0 (x) {
  return (x >>> 2 | x << 30) ^ (x >>> 13 | x << 19) ^ (x >>> 22 | x << 10)
}

function sigma1 (x) {
  return (x >>> 6 | x << 26) ^ (x >>> 11 | x << 21) ^ (x >>> 25 | x << 7)
}

function gamma0 (x) {
  return (x >>> 7 | x << 25) ^ (x >>> 18 | x << 14) ^ (x >>> 3)
}

function gamma1 (x) {
  return (x >>> 17 | x << 15) ^ (x >>> 19 | x << 13) ^ (x >>> 10)
}

Sha256.prototype._update = function (M) {
  var W = this._w

  var a = this._a | 0
  var b = this._b | 0
  var c = this._c | 0
  var d = this._d | 0
  var e = this._e | 0
  var f = this._f | 0
  var g = this._g | 0
  var h = this._h | 0

  for (var i = 0; i < 16; ++i) W[i] = M.readInt32BE(i * 4)
  for (; i < 64; ++i) W[i] = (gamma1(W[i - 2]) + W[i - 7] + gamma0(W[i - 15]) + W[i - 16]) | 0

  for (var j = 0; j < 64; ++j) {
    var T1 = (h + sigma1(e) + ch(e, f, g) + K[j] + W[j]) | 0
    var T2 = (sigma0(a) + maj(a, b, c)) | 0

    h = g
    g = f
    f = e
    e = (d + T1) | 0
    d = c
    c = b
    b = a
    a = (T1 + T2) | 0
  }

  this._a = (a + this._a) | 0
  this._b = (b + this._b) | 0
  this._c = (c + this._c) | 0
  this._d = (d + this._d) | 0
  this._e = (e + this._e) | 0
  this._f = (f + this._f) | 0
  this._g = (g + this._g) | 0
  this._h = (h + this._h) | 0
}

Sha256.prototype._hash = function () {
  var H = new Buffer(32)

  H.writeInt32BE(this._a, 0)
  H.writeInt32BE(this._b, 4)
  H.writeInt32BE(this._c, 8)
  H.writeInt32BE(this._d, 12)
  H.writeInt32BE(this._e, 16)
  H.writeInt32BE(this._f, 20)
  H.writeInt32BE(this._g, 24)
  H.writeInt32BE(this._h, 28)

  return H
}

//範例:

let hashString = new Sha256().digest('test','hex');

console.log(hashString)
```

使用Node.js的crypto模組進行驗證

```
var crypto = require('crypto');
let a = crypto.createHash('sha256').update('test').digest()
console.log(a.toString('hex'))
```

參考資料：

> http://www.iwar.org.uk/comsec/resources/cipher/sha256-384-512.pdf

# 





