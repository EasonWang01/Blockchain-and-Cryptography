# RSA

1977年由 Ron Rivest、Adi Shamir 和 Leonard Adleman 一起提出的。而RSA就是他們三人姓氏開頭字母拼在一起所組成。

而RSA的安全性建構在對一個極大的整數很難對其因數分解的情況下。

到目前為止，世界上還沒有任何可靠的攻擊RSA演算法的方式。只要其鑰匙的長度足夠長，用RSA加密的訊息實際上是不能被破解的。

---





RSA密鑰一般是1024bits或2048bits

第一步:隨便找兩個質數a b

> 質數表[http://www.baike.com/wiki/质数表](http://www.baike.com/wiki/质数表)

```
這裡假設a,b是23和29
```

第二步:計算φ\(axb\)           歐拉函數φ\(x\)的意思為小於x的所有質數的數量

```
23x29 = 667  
φ(667)根據歐拉定理得到為(23-1)*(29-1) = 616
```

第三步: 隨便找一個數字e，使得1&lt; e &lt; φ\(axb\)的數字，且e与φ\(axb\)互質

```
這裡選19
```

第四步: 找到一個數字d，使得 ed ≡ 1 \(mod φ\(n\)\)        意思為尋找 \(19\*X\) % 616  = 1

```
//以上面為例子
function te() {
  let X = 0
  while((19*X) % 616 !== 1) {
     X++;
   }
   return X
 }

 //回傳227
```

第五步: 剛才找出n = 667,  e = 19 , d = 227

得到公鑰為\(667, 19\)  私鑰為\(667, 227\)

> 如果n可以被輕易被因數分解，d就可以算出，而私鑰將被破解

#### \# 加密

假設字串是ABC，轉為**ASCII 為65,66,67 **

```
65,66,67
```

而msg輸入數字過大也會產生解密後和輸入數字不同之情況

> 建議將密文以單字為單位轉為ascII然後個別存入陣列轉換

所以我們把ABC分為三個65,66,67分別加解密再組合起來

1.先算字母A

這時加密的公鑰匙為\(667, 19\) 所以加密方式為

```
(65 的 19 次方) % 667
```

> 這裡因為數字是Big int所以算的時候不可直接用...\*\*...% ...，需要用相關Big INT模組不然會產生計算錯誤
>
> 不可用chrome devtool直接計算，結果會不同
>
> 建議可以用python的command，因其內建Bigint

```
打開command輸入python
然後(65 ** 19 ) % 667
```

得到數字451

#### \# 解密

私鑰匙是\(667, 227\)

現在要把剛才的數字 451 解密

```
(451**227) % 667
```

這裡牽涉到BIg integer時常會出現infinity

需要使用Big-integer module或是參考費馬小定理&gt;[https://zh.wikipedia.org/wiki/费马小定理](https://zh.wikipedia.org/wiki/费马小定理)

> 最後得到剛才的65即完成

以下為用js的big-integer模組，分別計算字母再連接的Function

```
var bigInt = require("big-integer");
let msg = "ABC";
let final = [];

msg.split('').forEach(char => {     // 每個字母分為Array一個元素
  let ascii = char.charCodeAt(0);  // 轉為ASCII
  let a = bigInt(ascii).pow(19).mod(667);   // 用公鑰加密
  let b = bigInt(a).pow(227).mod(667);    //// 用私鑰解密
  final.push(b.toString());
})

let decrypt = final.map(d => {
  return String.fromCharCode(parseInt(d)); // 轉為string
})

console.log(decrypt)
```

# 使用OpenSSL之RSA加解密

1.產生一個private key

> 預設產生長度為 512 bit  在最後可加數字1024 or 4096 產生不同長度私鑰

```
 openssl genrsa -out private.pem
```

2.產生PEM格式的公鑰

```
openssl rsa -in private.pem -out public.pem -outform PEM -pubout
```

3.產生一個稍後用來加密的檔案

```
vim test.txt

然後檔案內容隨意輸入
```

4.用公鑰加密test.txt檔案，將會產生encrypt.rsa檔案

```
openssl rsautl -encrypt -inkey public.pem -pubin -in test.txt -out encrypt.rsa
```

> 如檔案大小過大會產生錯誤

5.用私鑰解密檔案

```
openssl rsautl -decrypt -inkey private.pem -in encrypt.rsa -out decrypt.txt
```

6.最後查看解密後的檔案內容

```
cat decrypt.txt
```

即可看到成功還原為原本檔案之檔案內容

---

其他知識

```
DER (Distinguished Encoding Rules)：
二進位內容，屬於 ASN.1 制定的編碼之一

PEM (Privacy-enhanced Electronic Mail)：
為 DER 格式經過 BASE64 編碼後，通常會有 ----- BEGIN XXX ----- / ----- END XXX ----- 之類的東西包夾起來。由於 PEM 格式採用了 BASE64 編碼，文字都被編成常用的英文數字符號，方便於網路上傳送及複製 (如即時通訊、電子郵件等)。OpenSSL 預設產生的檔案都是 PEM 格式
```



