# 壓縮的地址

常聽到壓縮的地址意思為，壓縮後的Public Key產生之地址，因為橢圓曲線公式知道x後即可求得y，所以沒有必要把公鑰的x和y都加上去

未壓縮

```
04 + <x座標> + <y座標>
```

壓縮

```
02 or 03 + <x座標>
```

公鑰 Public Key 共包含65 bytes:

> > * 04 開頭
> > * 32-byte 之 x coordinate.
> > * 32-byte 之 y coordinate.
>
> 壓縮格式其實就是把 y coordinate丟棄，並且根據y coordinate 的最後一位是基數或偶數，如果是奇數則在開頭加上03，偶數加上02。

假設公鑰為：

```
04
eba3a6951a3b962ce05a5b3a2e562021fcfd7e2110b54250466d79a481b87120
2c8baff041a32ef8f5ea8cb0a6c4443ae8697acb98d98723d2eb4eb384326e49
```

則壓縮後的公鑰為：

```
03eba3a6951a3b962ce05a5b3a2e562021fcfd7e2110b54250466d79a481b87120
```

> 開頭03是因為 y coordinate最後一個字是奇數9。

#### 未壓縮公鑰轉為壓縮公鑰

```js
function Compress_public(_pubkey) {
  return (
    _pubkey.slice(-1) % 2 === 0
      ? "02" + _pubkey.slice(2, 66)
      : "03" + _pubkey.slice(2, 66)
  )
}
```

#### 壓縮公鑰轉回公鑰：

> npm install elliptic

```js
const elliptic = require('elliptic');
const ec = new elliptic.ec('secp256k1');
const publicKeyUncompressed = ec.keyFromPublic(填入壓縮公鑰, 'hex').getPublic(false, 'hex');
console.log(publicKeyUncompressed)
```

## 壓縮版本的私鑰

指的是壓縮版本的公鑰對應的私鑰，但為了識別，會在壓縮版本的私鑰最後加上`0x01`

```
Uncompressed private key:    
4F63BA6514E7EA4D2F561D96035729D93EDA4678F148A0CF8E8D77724B18E0B9

Compressed private key:    
4F63BA6514E7EA4D2F561D96035729D93EDA4678F148A0CF8E8D77724B18E0B901
```

> 可以看到後面多了`01`



