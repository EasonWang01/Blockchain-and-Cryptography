# DSA \( Digital Signature Algorithm \)

在1991年DSA 演算法被採納作為**Digital Signature Standard **\( **DSS **\)** **數位簽名標準之演算法。

DSA 的安全性是基於整數有限域離散對數之難題。

```
假設在Fp域上，已知a,t，可快速計算 (a ** t) mod p
但是假設已知a,b，欲求t ，則 a ** t = b mod p 則較難求解。
```

#### 參數選擇

```
1. 選擇一個Hash function 當作H  (e.g.SHA系列)

2. 選擇 L 和 N 的長度   FIPS 186-3 選定了 L 和 N 長度對為 (1024, 160), (2048, 224), (2048, 256), (3072, 256)。 
   N 必須小於等於 Hash function H 產生出來的長度.

3. 選擇一個 N-bit 的質數 q ( N 即為剛才選的數字)

4. 選擇 L-bit 的質數 p ( L 即為剛才選的數字)，並且 p − 1 是 q 的倍數
```

最後選擇一個 g ，滿足 $$g^q $$ = 1 mod p

之後這三個參數\(p, g, q\)可以公開分享。

#### 公鑰與私鑰

```
在0 < x < q 條件下任選一個適合長度的私鑰 x
```

之後公鑰即為$$y = g^x$$ mod p

#### 簽名

```
1. 在1 < k < q 條件下隨機產生一個k
2. 計算r， r = ((g ** k) mod p) mod q，假設r算出來為0則換一個k再算一次
3. 計算s， s = ((k ** -1) * (H(m) + xr)) mod q，假設s算出來為0則換一個k再算一次
```

得到簽名為\(r, s\)

#### 驗證

```
1. 如果0 < r < q或是0 < s < q則拒絕
2. 計算w， w = (s ** -1) mod q
3. 計算u1，u1 = H(m) * w mod q
4. 計算u2，u2 = (r * w) mod q
```

最後計算 v

![](/assets/螢幕快照 2018-01-07 上午11.50.11.png)

如果算出之 v = r 則驗證成功。

# 實際應用

# 使用OpenSSL

> 查看相關指令

```
openssl gendsa
```

![](/assets/94.png)

1.產生一個1024bits的參數檔案

```
openssl dsaparam -out dsa_param.pem 1024
```

2.從剛才的參數檔案產生一個private key 並且用AES-128之加密也可用AES-192或AES-256

> 密碼長度至少四位數

```
openssl gendsa -out dsa_privatekey.pem -aes128 dsa_param.pem
```

> 如出現 unable to write ''random state" 之訊息，可參考此
>
> [https://stackoverflow.com/questions/94445/using-openssl-what-does-unable-to-write-random-state-mean](https://stackoverflow.com/questions/94445/using-openssl-what-does-unable-to-write-random-state-mean)

3.接著再從私鑰產生一把公鑰

```
openssl dsa -in dsa_privatekey.pem -pubout -out dsa_publickey.pem
```

4.之後我們新增一個我們要用來簽章的文件 ，並且在裡面寫一點字

```
echo test > document.txt
```

然後用私鑰對文件簽章產生一個sig檔案

```
openssl dgst -dss1 -sign dsa_privatekey.pem -out document.sig document.txt
```

5.最後用公鑰驗證

```
openssl dgst -dss1 -verify dsa_publickey.pem -signature document.sig document.txt
```

![](/assets/89a.png)

## 使用Node.js

> 完成以上步驟產生pem格式之公私鑰後可執行以下程式。

```js
const fs = require('fs');
const crypto = require('crypto');

const privateKey = {key: fs.readFileSync('./dsa_privatekey.pem'), passphrase: "填入剛才輸入的密碼"};     
const publicKey = fs.readFileSync('./dsa_publickey.pem');

let sign = crypto.createSign('dsaWithSHA1');
sign.update('apple');

// 注意 這裡是用私鑰簽名，如果用公鑰會出現錯誤
let res = sign.sign(privateKey, 'hex');

let verify = crypto.createVerify('dsaWithSHA1');
verify.update('apple');

let result = verify.verify(publicKey, res, "hex");

console.log(result);  // Prints success. means the key pair works.
```



