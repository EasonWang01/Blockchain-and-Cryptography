# 4.nlockTime address

指定時間後該地址才可進行交易，可參考以下BIP：

[https://github.com/bitcoin/bips/blob/master/bip-0065.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0065.mediawiki)

其RedeemScript類似如下：

```
redeemScript = unixTime + OP_CHECKLOCKTIMEVERIFY + OP_DROP + publickey + OP_CHECKSIG;
```

程式範例：

```js
const crypto = require('crypto');
const ecdh = crypto.createECDH('secp256k1');
const bs58 = require('bs58');

// 查表
const OP_2 = "52";
const OP_3 = "53";
const OP_CHECKMULTISIG = "ae";

const publickey = "04b3a4408a6a42073864d78be08e1b474de48ee1c8c5b238e121c233d897fed86cb25ed2515b9f6b38ba88b18d4c0f1955aacc292ab4d2da9765b5d295ced5add5"
console.log('--公鑰--')
console.log(publickey);
console.log('-------------')
const unixTime = 1516985700 //自行設定，例如：Math.floor((Date.now() + 60 * 60 * 24 * 1000 * 7) / 1000) 時間設定在七天後，捨棄毫秒
const OP_CHECKLOCKTIMEVERIFY = "b1";
const OP_DROP = "75";
const OP_CHECKSIG = "ac";
// Push 65 bytes to stack = parseInt("65").toString(16) 65轉為16進位為41
const pubkeyBytes = "41"

const redeemScript = "04" + numToBuffer(unixTime).join('') + OP_CHECKLOCKTIMEVERIFY + OP_DROP + pubkeyBytes + publickey + OP_CHECKSIG;
console.log('--RedeemScript--')
console.log(redeemScript)
console.log('-------------')
ASCII_text = hex2ASCII(redeemScript); // 先將redeemScript轉為ASCII再放入SHA256 這邊可參考: https://bitcoin.stackexchange.com/a/43350

let hash = crypto.createHash('sha256').update(Buffer.from(ASCII_text, "ascii")).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();


//在publickeyHash前面加上一个05前缀
let version = new Buffer('05', 'hex');
let checksum = Buffer.concat([version, hash]);
//兩次256雙重加密
checksum = crypto.createHash('sha256').update(checksum).digest();
checksum = crypto.createHash('sha256').update(checksum).digest();

//取前4位得到效驗碼
checksum = checksum.slice(0, 4);
console.log('checksum')
console.log(checksum);
console.log('--------')

//把publickeyHash前面一樣加上00而後面加上剛才算出的checksum
let address = Buffer.concat([version, hash, checksum]);

// 最後使用base58進行編碼
address = bs58.encode(address);
console.log('編碼後的nlockTime比特幣地址')
console.log(address);
console.log('--------')

function hex2ASCII(_hex) {
  let hex = _hex.toString();
  let str = '';
  for (let i = 0; i < hex.length; i += 2) {
    str += String.fromCharCode(parseInt(hex.substr(i, 2), 16));
  }
  return str;
}

function numToBuffer(num) {
  if (num <= 256) {
    return [num.toString('16')];
  } else {
    let n = (num % 256).toString('16');
    let c = n.length === 1
      ? '0' + n // 16進位1~9補0在前面
      : n
    return [c].concat(numToBuffer(Math.floor(num / 256)));
  }
```

![](/assets/螢幕快照 2018-01-20 下午1.28.24.png)

# 5.HD \(hierarchical deterministic\) wallet address

參考下圖:

![](/assets/derivation.png)

> 圖片來源:[https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)

根據[BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 從12個隨機單字產生了128-bit master seed，之後繼續往下階層式的產生出許多地址

## 第一步驟

1.先用 seed 產生一個master key，而 seed 的長度需要是 128、256 或 512 bits

2.然後將 seed 去執行 HMAC-SHA512 加密，之後會產生一個長度為 512 bits 的結果

> 前 256 bits 為 master private key，後 256 bits 為 master chain code ，master chain code 代表 entropy\(熵\)，之後再往下產生 child keys 時會用到。

## 第二步驟

往下一層，產生該層各個child private key 的時候，要用到 child key derivation \(CKD\) 這個方法

[https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki\#child-key-derivation-ckd-functions](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#child-key-derivation-ckd-functions)

這個方法一樣是做HMAC-SHA512 加密演算法，但他會需要先輸入三個參數。

```
1.parent public key：拿上一層的 private key 來產生的 public key即是。
2.parent chain code：上一層key的 chain code，為一個entropy(熵)。
3.index：代表著產生下一層的第幾個 key，例如 0 則代表為下一層的第一個key。
```

> 之後會產生跟上層一樣的512bits的key，同樣的，前後256bits分別為 child private key 與 child chain code。

圖片來源:[https://github.com/bitcoinbook/bitcoinbook](https://github.com/bitcoinbook/bitcoinbook)

> 由於HMAC-SHA512是Hash function，過程是不可逆的，所以我們不會知道parent是什麼，以及也不會知道自己鄰近的其他child是什麼

下面我們會用到十六進位轉二進位，所以需下載『 big-integer-converter 』模組

```
npm install big-integer-converter
```

```js
const crypto = require('crypto');
const bigInt = require('big-integer-converter');
const ecdh = crypto.createECDH('secp256k1');

const seed = crypto.randomBytes(64)

HMACseed = crypto.createHmac('sha512', 'secret for HMAC').update(seed).digest('hex');
// 16進位轉為2進位
HMACseed_Binary = bigInt.hexToBin(HMACseed);

// 切割前後各256bits
Master_Prvkey = HMACseed_Binary.substring(0, 256);
Master_ChainCode = HMACseed_Binary.substring(256, 512);
var Master_pubkey = ecdh.setPrivateKey(bigInt.binToHex(Master_Prvkey), 'hex').getPublicKey('hex')
console.log('---Master公鑰---')
console.log(Master_pubkey); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)
console.log('-----------------')

function CKD(parentPub, parentChainCode, childIdx) {
  let input = parentPub + parentChainCode + childIdx;
  return crypto.createHmac('sha512', 'secret for HMAC').update(input).digest('hex')
}

// 第一層derivation的第一個child
let derivation0_child0 = CKD(Master_pubkey, Master_ChainCode, '00000000'); // 第三個參數，因index number規定要32bits，所以填入十六進位的八個數字
// 第一層derivation的第二個child
let derivation0_child1 = CKD(Master_pubkey, Master_ChainCode, '00000001');
console.log('---第一層derivation的第一個child---')
console.log(derivation0_child0) //長度為十六進位的128個字，所以為512bits
console.log('---第一層derivation的第二個child---')
console.log(derivation0_child1)
console.log('-----------------')

// 下面我們再示範用第一層的第一個child產生第二層
console.log('---第一層的第一個child的public key （m/0）---')
derivation0_child0_Prvkey = derivation0_child0.substring(0, 64); // 因為十六進位，所以取一半是取到第64個字 相當於256bits
var derivation0_child0Pub = ecdh.setPrivateKey(derivation0_child0_Prvkey, 'hex').getPublicKey('hex')
console.log(derivation0_child0Pub)
console.log('------')

console.log('---第一層的第一個child的chain code （m/0）---')
derivation0_child0_ChainCode = derivation0_child0.substring(64, 128); //取後面的一半
console.log(derivation0_child0_ChainCode)
console.log('------')

//第二層
let derivation1_child1 = CKD(derivation0_child0Pub, derivation0_child0_ChainCode, '00000001');
let derivation1_child10 = CKD(derivation0_child0Pub, derivation0_child0_ChainCode, '00000010');

console.log('-----------------')
console.log('---第一層derivation的第一個child長出來之第二層的第一個child (m/0/0)---')
console.log(derivation1_child1) //長度為十六進位的128個字，所以為512bits
console.log('---第一層derivation的第一個child長出來之第二層的第十個child (m/0/11)---')
console.log(derivation1_child10)
console.log('-----------------')
```

#### xPrv與xPub

假設我們要用一個字串表示parent public key 和 parent chain code 可以把他們用一種Extended key 82 byte structure方法結合起來並且使用Base58進行編碼，之後可以產生出xPrv與xPub，如果對其進行Base58解碼則可以得到這個Key所在的位置以及其相關資訊。

Extended key 之 82 byte structure 其結合元素包含以下

```
4 byte: version bytes (mainnet: 0x0488B21E public, 0x0488ADE4 private; testnet: 0x043587CF public, 0x04358394 private)
1 byte: depth: 0x00 for master nodes, 0x01 for level-1 derived keys, ....
4 bytes: the fingerprint of the parent's key (0x00000000 if master key)
4 bytes: child number. This is ser32(i) for i in xi = xpar/i, with xi the key being serialized. (0x00000000 if master key)
32 bytes: the chain code
33 bytes: the public key or private key data (serP(K) for public keys, 0x00 || ser256(k) for private keys)

4 bytes: 最後再把上面做checksum，然後加在字串最後。
```

> [https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki\#serialization-format](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#serialization-format)

從Master 512 bits seed Hash產生Master之xPrv與xPub

```js
const crypto = require('crypto');
const bs58 = require('bs58');

function hexToDecimalArray(s) {
  if (s[0] + s[1] === '0x') {
    s = s.replace('0x', '');
  }
  let c = [];
  for (let i = 0; i < s.length - 1; i += 2) {
    c.push(parseInt(`${s[i] + s[i + 1]}`, 16));
  }
  return c;
}

// 4 bytes version 
// 0x0488B21E  mainnet public，
// 0x0488ADE4  mainnet private，
// 0x043587CF  testnet public，
// 0x04358394  testnet private
const mainnet_pri = '0x0488ADE4'; 
const mainnet_pub = '0x0488b21e';
const depth = 0;  // 1 bytes
const parent_fingerprint = [0, 0, 0, 0]; // 4 bytes
const child_index = [0, 0, 0, 0]; // 4 bytes
let chain_code = ''; // hexToDecimalArray(parent.slice(0, 64))
let private_key = ''; // hexToDecimalArray(parent.slice(64, 128))

const generate_xkey = (parent, _keyType, pubkey) => (
  [...hexToDecimalArray(_keyType),
    depth,
  ...parent_fingerprint,
  ...child_index,
  ...hexToDecimalArray(parent.slice(64, 128)), // chain code
  _keyType === mainnet_pri ? 0 : null, // 如果為xPrv要加上0
  ...hexToDecimalArray(_keyType === mainnet_pri ? parent.slice(0, 64) : pubkey), // key
  ].filter(n => n !== null) // 將 null 元素移除
)

// 512 bits 的 Parent Hash
Parent_HASH = "f302ca1da51bc8aa62004bcab75c83d67d578d498a7dd79d884a7f92759b611be41a13d838849258704f0265276fa6655c8961dfc7da1535d5cba7f98c5402ff";
/*
也可用以下方式產生512bits master seed
const seed = crypto.randomBytes(64)
HMACseed = crypto.createHmac('sha512', 'secret for HMAC').update(seed).digest('hex');
*/


/**
 * 產生xPrv
 */

let xPrv_Bytes = generate_xkey(Parent_HASH, mainnet_pri);

checksum = crypto.createHash('sha256').update(Buffer.from(xPrv_Bytes)).digest()
checksum = crypto.createHash('sha256').update(checksum).digest().slice(0, 4)
checksumArray = []
checksum.forEach(buf => {
  checksumArray.push(buf)
})

let xPrv_array = xPrv_Bytes.concat(checksumArray)

let xPrv = bs58.encode(xPrv_array)
console.log('--xPrv--')
console.log(xPrv)


/**
 * 產生xPub
 */


const ecdh = crypto.createECDH('secp256k1');
// 64個hex，256bits
pri_key = Parent_HASH.slice(0, 64);

// 從私鑰產生出公鑰
const Uncompress_publickey = ecdh.setPrivateKey(Buffer.from(hexToDecimalArray(pri_key))).getPublicKey('hex')
Compressed_publickey = Compress_public(Uncompress_publickey);

// 變為壓縮的公鑰
function Compress_public(_pubkey) {
  return (
    _pubkey.slice(-1) % 2 === 0
      ? "02" + _pubkey.slice(2, 66)
      : "03" + _pubkey.slice(2, 66)
  )
} 

// 之後轉為xpub
let xPub_Bytes = generate_xkey(Parent_HASH, mainnet_pub, Compressed_publickey);
checksum = crypto.createHash('sha256').update(Buffer.from(xPub_Bytes)).digest()
checksum = crypto.createHash('sha256').update(checksum).digest().slice(0, 4)
checksumArray = []
checksum.forEach(buf => {
  checksumArray.push(buf)
})

let xPub_array = xPub_Bytes.concat(checksumArray)
let xPub = bs58.encode(xPub_array)
console.log('--xPub--')
console.log(xPub)
```

![](/assets/ijosd9.png)

參考自:

> [https://github.com/sarchar/brainwallet.github.com/tree/bip32](https://github.com/sarchar/brainwallet.github.com/tree/bip32)
>
> [https://github.com/OutCast3k/coinbin/](https://github.com/OutCast3k/coinbin/)

#### 從xPrv或xPub往下一層計算child xPrv 與 xPub

可參考:[https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki\#child-key-derivation-ckd-functions](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#child-key-derivation-ckd-functions)

> 注意 : 無法從Public parent key 算出 private child key

```js
const ecc = require('elliptic-utility').Elliptic
const BigInteger = require('elliptic-utility').BigInteger
const crypto = require('crypto');
const base58 = require('bs58')
// 可替換其他xpub 或 xprv
const parent_xkey = 'xprv9s21ZrQH143K3Cbeqiamwsa6TBF3jEYsLKbYTMF1zN2qFz7hSPWtdDe1q8EmF1e4eYjMPt5zz83wgJK6WhHuKpWFjtitVJpvWVRhSkDxAoE';

// 解碼xkey
function decode_xkey(data) {
    if (typeof (data) == 'string') {
        var decoded = base58.decode(data)
        if (decoded.length == 82) {
            var decoded_checksum = decoded.slice(78, 82);
            // 重新檢查checksum
            doubleSHA_bytes = crypto.createHash('sha256').update(
                crypto.createHash('sha256').update(Buffer.from(decoded.slice(0, 78))).digest()
            ).digest();
            if (doubleSHA_bytes[0] == decoded_checksum[0]
                && doubleSHA_bytes[1] == decoded_checksum[1]
                && doubleSHA_bytes[2] == decoded_checksum[2]
                && doubleSHA_bytes[3] == decoded_checksum[3]) {
                let _bytes = decoded.slice(0, 78);
                return _bytes
            } else {
                console.log('checksum not match');
            }
        }
    }
}
bytes = decode_xkey(parent_xkey)

// 將base58 decode後的bytes 分解為六部分共78bytes
let ser = {
    version: bytes.slice(0, 4),
    depth: bytes.slice(4, 5),
    parent_fingerprint: bytes.slice(5, 9),
    child_index: bytes.slice(9, 13),
    chain_code: bytes.slice(13, 45),
    key_bytes: bytes.slice(45, 78)
};


//  如果xkey 為 xprv 則解碼出private key 並利用橢圓曲線產生public key 
if (ser.key_bytes[0] === 0) {
    // private key
    private_key = bytesToHex(ser.key_bytes.slice(1, 33));
    // 從私鑰產生公鑰
    const ecdh = crypto.createECDH('secp256k1');
    const Uncompress_publickey = ecdh.setPrivateKey(Buffer.from(hexToDecimalArray(private_key))).getPublicKey('hex')
    // 變為壓縮的公鑰
    function Compress_public(_pubkey) {
        return (
            _pubkey.slice(-1) % 2 === 0
                ? "02" + _pubkey.slice(2, 66)
                : "03" + _pubkey.slice(2, 66)
        )
    }
    public_key = Compress_public(Uncompress_publickey);
} else if (ser.key_bytes[0] === 2 || ser.key_bytes[0] === 3) {
    //  如果為 xkey 為 xpub 則指產生public key
    public_key = bytesToHex(ser.key_bytes);
} else {
    console.log('invalid key');
}

let index = "00000000"; // 這邊預設都是只產生index為0的child
let HMAC_data = public_key + index;
let HMAC_key = ser.chain_code;

// 使用 parent 之 public_key 與 chain code 產生 HMAC_SHA512雜湊
HMAC_SHA512 = crypto.createHmac('sha512', Buffer.from(HMAC_key, 'hex'))
    .update(Buffer.from(HMAC_data, 'hex')).digest('hex');
// il與ir為HMAC-SHA512 雜湊後切分左側與右側
let il = new BigInteger(HMAC_SHA512.slice(0, 64), 16);
let ir = HMAC_SHA512.slice(64, 128);

// 相關橢圓曲線參數使用secp256k1
EllipticCurve = ec;
var ecparams = ec.getSECCurveByName("secp256k1");
var curve = ecparams.getCurve();

if (parent_xkey.slice(0, 4) === 'xprv') {
    // 使用父 private_key 計算derive 出的 下一層 child private key 與 child public key
    k = il.add(new BigInteger([0].concat(hexToDecimalArray(private_key)))).mod(ecparams.getN());
    child_privatekey = bytesToHex(k.toByteArrayUnsigned());
    // pubkey = coinjs.newPubkey(key);
    // 從私鑰產生公鑰
    const ecdh = crypto.createECDH('secp256k1');
    const Uncompress_publickey = ecdh.setPrivateKey(Buffer.from(hexToDecimalArray(child_privatekey))).getPublicKey('hex')
    child_publickey = Compress_public(Uncompress_publickey);

} else if (parent_xkey.slice(0, 4) === 'xpub') {
    //  使用 父 public_key 計算 derive 出的 下一層 child public key
    q = ecparams.curve.decodePointHex(public_key);
    var curvePt = ecparams.getG().multiply(il).add(q);
    var x = curvePt.getX().toBigInteger();
    var y = curvePt.getY().toBigInteger();
    var x_decimalBytes = EllipticCurve.integerToBytes(x, 32);// toByteArrayUnsigned 與 integerToBytes 類似
    var y_decimalBytes = EllipticCurve.integerToBytes(y, 32);

    //     轉為壓縮公鑰
    var publicKeyBytesCompressed = x_decimalBytes
    if (y_decimalBytes[y_decimalBytes.length - 1] % 2 === 0) {
        publicKeyBytesCompressed.unshift(0x02)
    } else {
        publicKeyBytesCompressed.unshift(0x03)
    }
    // 從 bytes格式轉為Hex字串格式的child public key
    child_publickey = bytesToHex(publicKeyBytesCompressed)
} else {
    console.log('Not a valid xkey');
}


// 用 parent 的 public key 算出 finger_print
var RIPEMD160 = require('ripemd160')
let sha256 = crypto.createHash('sha256').update(Buffer.from(public_key, 'hex')).digest('hex')
let fingerprint = hexToDecimalArray(new RIPEMD160().update(Buffer.from(sha256, 'hex')).digest('hex')).slice(0, 4);


// 4 bytes version
// 0x0488B21E mainnet public，
// 0x0488ADE4 mainnet private，
// 0x043587CF testnet public，
// 0x04358394 testnet private
const mainnet_prv = '0x0488ADE4';
const mainnet_pub = '0x0488b21e';

// 之後轉為xpub
var child_index = [0, 0, 0, 0] // 這邊預設產生index為0的child

function derive(type, _) {
    let _bytes = generate_xkey(type);
    checksum = crypto.createHash('sha256').update(Buffer.from(_bytes)).digest()
    checksum = crypto.createHash('sha256').update(checksum).digest().slice(0, 4)
    checksumArray = []
    checksum.forEach(buf => {
        checksumArray.push(buf)
    })
    _bytes = _bytes.concat(checksumArray)
    let xkey = base58.encode(_bytes)
    console.log(_)
    console.log(xkey)
}

derive(mainnet_pub, '--xPub--');
if (parent_xkey.slice(0, 4) === 'xprv') {
    // 只有在輸入為xprv時才會產生 child xprv
    derive(mainnet_prv, '--xPrv--');
}

/////////////////////////////////////////////

function generate_xkey(_keyType) {
    return (
        [...hexToDecimalArray(_keyType),
            1,  //depth
        ...fingerprint,
        ...child_index, // child_index
        ...hexToDecimalArray(ir), // chain code
        _keyType === mainnet_prv ? 0 : null, // 如果為xPrv要加上0
        ...hexToDecimalArray(_keyType === mainnet_prv ? child_privatekey : child_publickey), // key
        ].filter(n => n !== null) // 將 null 元素移除
    )
}

function bytesToHex(byteArray) {
    let _s = '';
    byteArray.forEach(byte => {
        _s += byte.toString('16').length === 1
            ? `0${byte.toString('16')}`
            : byte.toString('16')
    })
    return _s
}
function hexToDecimalArray(s) {
    if (s[0] + s[1] === '0x') {
        s = s.replace('0x', '');
    }
    let c = [];
    for (let i = 0; i < s.length - 1; i += 2) {
        c.push(parseInt(`${s[i] + s[i + 1]}`, 16));
    }
    return c;
}
```

#### 使用第三方模組

以下我們使用Bitcore模組為範例

```
npm install bitcore
```

```js
var bitcore = require('bitcore');
var HDPrivateKey = bitcore.HDPrivateKey;

var hdPrivateKey = new HDPrivateKey();
console.log(hdPrivateKey.toString())

var derived_m_0 = hdPrivateKey.derive("m/0");
var derivedByNumber_1_2 = hdPrivateKey.derive(1).derive(2);
var derivedByArgument_1_2 = hdPrivateKey.derive("m/1/2");
console.log(derivedByNumber_1_2);
console.log(derivedByArgument_1_2)

// Hardened
var derivedByNumber_1_2 = hdPrivateKey.derive(1).derive(2, true);
var derivedByArgument_1_2 = hdPrivateKey.derive("m/1/2'");
console.log(derivedByNumber_1_2);
console.log(derivedByArgument_1_2)

var address = derived_m_0.privateKey.toAddress();
console.log(address)

// obtain HDPublicKey
var hdPublicKey = hdPrivateKey.hdPublicKey;
console.log(hdPublicKey.toString())
```

> child key 可分為 Hardened 與 non-Hardened
>
> 用parent private key與 chaincode 雜湊產生 key稱為Hardened key
>
> 用parent public key與 chaincode 雜湊產生 key稱為 non-Hardened key
>
> 假設我們今天想建立許多地址收款，但不想要給每個地址知道對應私鑰，減少被駭客竊取私鑰的機會，則可以選擇non-Hardened key，因為其不會產生私鑰。
>
> 但non-Hardened 有缺點如下
>
> ```
> 知道parent extended public key 以及任何一個 child non-hardened private key 
> 即可推導出 parent extended private key 以及任何 child private key 及 public key  
> // 可參考: https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/test/integration/crypto.js#L115
> // https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#security
>
> 所以non-Hardened 的 parent extended public keys 不可以洩漏出去，而 hardened keys 可以解決此問題。
>
> 一般 child keys index 為 0 到 (2 ** 31) - 1 ，而 hardened child keys 的 index為 2 ** 31 到 (2 ** 32) - 1
> ```

[https://bitcoin.stackexchange.com/questions/56916/derivation-of-parent-private-key-from-non-hardened-child](https://bitcoin.stackexchange.com/questions/56916/derivation-of-parent-private-key-from-non-hardened-child)

# 查看地址的相關資料與交易紀錄

[https://blockchain.info/rawaddr/1GbVUSW5WJmRCpaCJ4hanUny77oDaWW4to](https://blockchain.info/rawaddr/1GbVUSW5WJmRCpaCJ4hanUny77oDaWW4to)

```js
var http = require('http');
http.get({
    host: 'blockchain.info',
    path: '/rawaddr/1GbVUSW5WJmRCpaCJ4hanUny77oDaWW4to'
}, function (response) {
    var body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        console.log(body);
    });
});
```

會出現如下訊息

```json
{
    "hash160":"ab0fcc2fb04ee80d29a00a80140b16323bed3d6e",
    "address":"1GbVUSW5WJmRCpaCJ4hanUny77oDaWW4to",
    "n_tx":3589,
    "total_received":4629912224989,
    "total_sent":4577511878052,
    "final_balance":52400346937,
    "txs":[

{
   "ver":1,
   "inputs":[
      {
         "sequence":4294967295,
         "witness":"01200000000000000000000000000000000000000000000000000000000000000000",
         "script":"03b58f070004fcfd145a0450e2b80708985d085a00001e06092f426974667572792f"
      }
   ],
   "weight":700,
   "block_height":495541,
   "relayed_by":"0.0.0.0",
   "out":[
      {
         "spent":false,
         "tx_index":303487002,
         "type":0,
         "addr":"1GbVUSW5WJmRCpaCJ4hanUny77oDaWW4to",
         "value":1284013028,
         "n":0,
         "script":"76a914ab0fcc2fb04ee80d29a00a80140b16323bed3d6e88ac"
      },

      .....
```

# 地址不建議重複使用

地址如果重複使用容易造成個人隱私上的暴露，所以許多交易所的地址會經常更換，其他相關資訊可參考如下連結:

[https://en.bitcoin.it/wiki/Address\_reuse](https://en.bitcoin.it/wiki/Address_reuse)

