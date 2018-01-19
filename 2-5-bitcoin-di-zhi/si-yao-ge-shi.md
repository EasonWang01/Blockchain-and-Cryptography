# 私鑰格式

比特幣的私鑰長度為 256 bits\(32 bytes\) 在十六進位為 64 個字。

任何在以下範圍中的私鑰都可以是合法的。

```
0x1 到 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4140
```

# WIF\(Wallet import format\)

一般私鑰跟WIF格式私鑰可以互相轉換，其優點在於長度稍微縮短，以及加入了checksum

可參考以下網站在線轉換[http://gobittest.appspot.com/PrivateKey](http://gobittest.appspot.com/PrivateKey)

## 一般私鑰轉為WIF格式

```js
var crypto = require('crypto');
var bs58 = require('bs58');

// 隨機的私鑰
const privateKey = "0C28FCA386C7A227600B2FE50B7CAE11EC86D3BF1FBE471BE89827E19D72AA1D";

// 前面加上0x80
var add0x80_front = "80" + privateKey;

// 使用double sha256   後面.update使用hex原因是nodejs預設v6版本之後預設是utf-8
// https://stackoverflow.com/questions/37213211/why-crypto-createhash-returns-different-output-in-new-version
const sha256Value1 = crypto.createHash('sha256').update(add0x80_front, 'hex').digest();
const sha256Value2 = crypto.createHash('sha256').update(sha256Value1, 'hex').digest();

// 取前八位當作checksum
const first4byte = sha256Value2.toString('hex').substring(0,8);

// 串接
const concatString = add0x80_front + first4byte;
WIF_PrivateKey = bs58.encode(new Buffer(concatString, 'hex'));
console.log(WIF_PrivateKey)
```

## WIF格式私鑰轉換為原本私鑰格式

```js
var crypto = require('crypto');
var bs58 = require('bs58');

// WIF格式之私鑰
const WIF_privateKey = "5HueCGU8rMjxEXxiPuD5BDku4MkFqeZyd4dZ1jvhTVqvbTLvyTJ";

// base58 decode
const decodeWIF = bs58.decode(WIF_privateKey);

// 把最後四位checksum丟棄
const dropLast4 = decodeWIF.slice(0,33);

// 把第一個byte丟棄
const final = decodeWIF.slice(1, dropLast4.length);
console.log(final)
```

### WIF­ compressed格式

私鑰其實沒有壓縮格式，只有公鑰可以壓縮，而WIF­ compressed的意思為代表其為壓縮格式公鑰的私鑰，只要在一般的公鑰最後面加上0x01之後一樣使用WIF壓縮即可

> 壓縮格式的WIF開頭為**K或L**

![](/assets/896.png)

#### 從WIF私鑰轉為比特幣地址

```js
const bitcoin = require('bitcoinjs-lib')
const keyPair = bitcoin.ECPair.fromWIF('5JdT9kXDHJi3zzyJfYhvpWDAQrk6y7GPCkQiQfdxk5aV4jrq12A') // 輸入WIF格式私鑰
const address = keyPair.getAddress()
console.log(address)
```

# Mini Private Key

> 開頭為S，然後長度為30個字
>
> 產生出來的mini key會在最後加上`?`號然後進行sha256，確認前兩個byte是否為00，如果是才為合法的Mini Key
>
> 以下使用Python程式為範例

```python
import random
import hashlib

BASE58 = '23456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz'


def Candidate():
    """
    Generate a random, well-formed mini private key.
    """
    return('%s%s' % ('S', ''.join(
        [BASE58[ random.randrange(0,len(BASE58)) ] for i in range(29)])))

def GenerateKeys(numKeys = 10):
    """
    Generate mini private keys and output the mini key as well as the full
    private key. numKeys is The number of keys to generate, and 
    """
    keysGenerated = 0
    totalCandidates = 0
    while keysGenerated < numKeys:
        try:
            cand = Candidate()
            # Do typo check
            t = '%s?' % cand
            # Take one round of SHA256
            candHash = hashlib.sha256(t).digest()
            # Check if the first eight bits of the hash are 0
            if candHash[0] == '\x00':
                privateKey = GetPrivateKey(cand)
                print('\n%s\nSHA256( ): %s\nsha256(?): %s' %
                      (cand, privateKey, candHash.encode('hex_codec')))
                if CheckShortKey(cand):
                    print('Validated.')
                else:
                    print('Invalid!')
                keysGenerated += 1
            totalCandidates += 1
        except KeyboardInterrupt:
            break
    print('\n%s: %i\n%s: %i\n%s: %.1f' %
          ('Keys Generated', keysGenerated,
           'Total Candidates', totalCandidates,
           'Reject Percentage',
           100*(1.0-keysGenerated/float(totalCandidates))))

def GetPrivateKey(shortKey):
    """
    Returns the hexadecimal representation of the private key corresponding
    to the given short key.
    """
    if CheckShortKey(shortKey):
        return hashlib.sha256(shortKey).hexdigest()
    else:
        print('Typo detected in private key!')
        return None

def CheckShortKey(shortKey):
    """
    Checks for typos in the short key.
    """
    if len(shortKey) != 30:
        return False
    t = '%s?' % shortKey
    tHash = hashlib.sha256(t).digest()
    # Check to see that first byte is \x00
    print(tHash[1])
    if tHash[0] == '\x00':
        return True
    return False

GenerateKeys()
```



