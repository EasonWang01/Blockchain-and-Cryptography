# ECDSA程式實作

#### 原理：

因為以下程式涉及比較多大數運算，但Javascript因為沒有內建Big-integer之處理，但Python有內建Big-integer，不會導致數字較大時精度跑掉，所以以下範例以Python來展示 \( version 2.7.14 \)。

> 以下程式為無使用ECDSA相關第三方套件之範例

```python
# coding=utf-8

# The proven prime
Pcurve = 2**256 - 2**32 - 2**9 - 2**8 - \
    2**7 - 2**6 - 2**4 - 1  

# Number of points in the field
N = 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141

# This defines the curve. y^2 = x^3 + Acurve * x + Bcurve
Acurve = 0
Bcurve = 7  

Gx = 55066263022277343669578718895168534326250603453777594175500187360389116729240
Gy = 32670510020758816978083085130507043184471273380659243275938904335757337482424
GPoint = (Gx, Gy)  # This is our generator point. Tillions of dif ones possible

# Individual Transaction/Personal Information
# replace with any private key
privKey = 75263518707598184987916378021939673586055614731957507592904438851787542395619
# replace with a truly random number
RandNum = 28695618543805844332113829720373285210420739438570883203839696518176414791234
# the hash of your message/transaction
HashOfThingToSign = 86032112319101611046176971828093669637772856272773459297323797145286374828050


def modinv(a, n=Pcurve):  # Extended Euclidean Algorithm/'division' in elliptic curves
    lm, hm = 1, 0
    low, high = a % n, n
    while low > 1:
        ratio = high / low
        nm, new = hm - lm * ratio, high - low * ratio
        lm, low, hm, high = nm, new, lm, low
    return lm % n


# Not true addition, invented for EC. It adds Point-P with Point-Q.
def ECadd(xp, yp, xq, yq):
    m = ((yq - yp) * modinv(xq - xp, Pcurve)) % Pcurve
    xr = (m * m - xp - xq) % Pcurve
    yr = (m * (xp - xr) - yp) % Pcurve
    return (xr, yr)


# EC point doubling, invented for EC. It doubles Point-P.
def ECdouble(xp, yp):
    LamNumer = 3 * xp * xp + Acurve
    LamDenom = 2 * yp
    Lam = (LamNumer * modinv(LamDenom, Pcurve)) % Pcurve
    xr = (Lam * Lam - 2 * xp) % Pcurve
    yr = (Lam * (xp - xr) - yp) % Pcurve
    return (xr, yr)


def EccMultiply(xs, ys, Scalar):  # Double & add. EC Multiplication, Not true multiplication
    if Scalar == 0 or Scalar >= N:
        raise Exception("Invalid Scalar/Private Key")
    ScalarBin = str(bin(Scalar))[2:]
    Qx, Qy = xs, ys
    for i in range(1, len(ScalarBin)):  # This is invented EC multiplication.
        Qx, Qy = ECdouble(Qx, Qy)  # print "DUB", Qx; print
        if ScalarBin[i] == "1":
            Qx, Qy = ECadd(Qx, Qy, xs, ys)  # print "ADD", Qx; print
    return (Qx, Qy)


print
print "******* Public Key Generation *********"
xPublicKey, yPublicKey = EccMultiply(Gx, Gy, privKey)
print "the private key (in base 10 format):"
print privKey
print
print "the public key:"
print  "xPublicKey:", xPublicKey
print  "yPublicKey:", yPublicKey
print

print "******* Signature Generation *********"
xRandSignPoint, yRandSignPoint = EccMultiply(Gx, Gy, RandNum)
r = xRandSignPoint % N
print "r =", r
s = ((HashOfThingToSign + r * privKey) * (modinv(RandNum, N))) % N
print "s =", s

print
print "******* Signature Verification *********>>"
w = modinv(s, N)
xu1, yu1 = EccMultiply(Gx, Gy, (HashOfThingToSign * w) % N)
xu2, yu2 = EccMultiply(xPublicKey, yPublicKey, (r * w) % N)
x, y = ECadd(xu1, yu1, xu2, yu2)
print r == x
print
```

之後會顯示如下訊息

![](/assets/85.png)

# 使用OpenSSL產生金鑰並用Node.js簽名與驗證

1.首先我們使用以下指令產生私鑰

```
openssl ecparam -name secp256k1 -genkey -noout -out ec_private_key.pem
```

> 產生之私鑰檔案內容如下，此檔案一般不可公開。![](/assets/n.png)

2.從私鑰來產生對應的公鑰

```
openssl ec -in ec_private_key.pem -pubout -out ec_public_key.pem
```

> 產生公鑰檔案內容如下![](/assets/nn.png)

3.使用Node.js讀取私鑰與公鑰，並進行簽名與驗證

```js
const crypto = require('crypto');

const fs = require('fs')
const sign = crypto.createSign('ecdsa-with-SHA1');

sign.update('test');
const privateKey = fs.readFileSync('./ec_private_key.pem') // 讀取私鑰
signature = sign.sign(privateKey, 'hex');

const verify = crypto.createVerify('ecdsa-with-SHA1');
verify.update('test');
const publicKey = fs.readFileSync('./ec_public_key.pem') // 讀取公鑰

console.log(verify.verify(publicKey, signature, 'hex'));
```

# 使用Node.js第三模組產生公鑰與私鑰並簽名及驗證

我們使用 jsrsasign 模組 [https://kjur.github.io/jsrsasign/](https://kjur.github.io/jsrsasign/)

```
npm install jsrsasign
```

使用範例

```js
const r = require('jsrsasign');
const ec = new r.ECDSA({ 'curve': 'secp256r1' });
const keypair = ec.generateKeyPairHex(); // 產生公鑰與私鑰
const prvhex = keypair.ecprvhex; // hexadecimal string of EC private key
const pubhex = keypair.ecpubhex; // hexadecimal string of EC public key

console.log("----Private Key:----")
console.log(prvhex, "\n")
console.log("----Public Key:-----")
console.log(pubhex, "\n") 

const message = "test";

const sig = new r.Signature({ "alg": 'SHA256withECDSA' });
sig.init({ d: prvhex, curve: 'secp256r1' });
sig.updateString(message);
const sigValueHex = sig.sign();   // 進行簽名
console.log("----signature:-----")
console.log(sigValueHex, "\n");
console.log("-------------------")

const sig_pub = new r.Signature({ "alg": 'SHA256withECDSA' });
sig_pub.init({ xy: pubhex, curve: 'secp256r1' });
sig_pub.updateString(message);
const result = sig_pub.verify(sigValueHex);  // 進行驗證
if (result) {
    console.log("valid ECDSA signature");
} else {
    console.log("invalid ECDSA signature");
}
```

之後可看到如下輸出:

![](/assets/84.png)

# 使用Node.js之原生方式產生公私鑰

> 使用如下方法也可以產生相同曲線的公私鑰
>
> ```js
> const crypto = require('crypto');
> const bob = crypto.createECDH('secp256k1');
> bob.generateKeys();
> prvhex = bob.getPrivateKey().toString('hex')
> pubhex = bob.getPublicKey().toString('hex')
> ```

完成範例

```js
// 使用Node.js的ECDH來產生公鑰與私鑰
const crypto = require('crypto');
const bob = crypto.createECDH('secp256k1');
bob.generateKeys();
prvhex = bob.getPrivateKey().toString('hex')
pubhex = bob.getPublicKey().toString('hex')
console.log(prvhex)
console.log(pubhex)

message = "test";

// 使用jsrsasign 的Signature函式，並用同樣之secp256k1 curve來進行sign的動作
var r = require('jsrsasign');
var ec = new r.ECDSA({ 'curve': 'secp256k1' });

var sig = new r.Signature({ "alg": 'SHA256withECDSA' });
sig.init({ d: prvhex, curve: 'secp256k1' });
sig.updateString(message);
var sigValueHex = sig.sign();

var sig = new r.Signature({ "alg": 'SHA256withECDSA' });
sig.init({ xy: pubhex, curve: 'secp256k1' });
sig.updateString(message);
var result = sig.verify(sigValueHex);
if (result) {
    console.log("valid ECDSA signature");
} else {
    console.log("invalid ECDSA signature");
}
```

---

## OpenSSL 指令列操作Elliptic\_Curve

以下我們產生一個secp256k1的Key

```
openssl ecparam -name secp256k1 -genkey -out ec-key.pem
```

之後我們可以用以下指令讀取

> 會顯示出Private Key與Public key

```
openssl ec -in ec-key.pem -text
```

![](/assets/jas90.png)

接著我們可以把Public Key獨立取出:

```
openssl ec -in ec-priv.pem -pubout -out ec-pub.pem
```

顯示Public Key:

```
openssl ec -in ec-pub.pem -pubin -text
```

> 公鑰 Public Key 共包含65 bytes:
>
> * 04 開頭
> * 32-byte 之 x coordinate.
> * 32-byte 之 y coordinate.

轉為壓縮格式的公鑰

```
openssl ec -in ec-pub.pem -pubin -text -out compress-pub.pem -conv_form compressed
```

> 壓縮格式其實就是把 y coordinate丟棄，並且根據y coordinate 的最後一位是基數或偶數，如果是奇數則在開頭加上03，偶數加上02

![](/assets/ksd9.png)

[https://wiki.openssl.org/index.php/Command\_Line\_Elliptic\_Curve\_Operations](https://wiki.openssl.org/index.php/Command_Line_Elliptic_Curve_Operations)

