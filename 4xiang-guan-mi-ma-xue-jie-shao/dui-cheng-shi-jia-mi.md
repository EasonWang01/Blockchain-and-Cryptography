# 對稱式加密\(**Symmetric-key algorithm**\)

在加密和解密時使用相同的密鑰，常見的對稱是加密算法有：AES、Serpent、3DES、IDEA、TwoFish、RC系列等等

而DES與BlowFish目前因安全性已不建議採用。

而下面展示一個對稱式加密最基本的原理:

```
密文: Test
密鑰: 11110011
```

我們先把密文轉換為ACSII 二進位

```js
function textToBinary(string) {
    return string.split('').map(function (char) {
        return '0' + char.charCodeAt(0).toString(2);
    })
}

textToBinary("Test");
// ["01010100", "01100101", "01110011", "01110100"]
```

上面每個陣列對應一個英文字母，我們把每個陣列元素與密鑰分別做XOR運算\(註1\):

```js
function XOR(a, b) {
 if(a.length !== b.length) {
   console.log("要求兩者二進位數字長度相同");
   return
 }
 let num = (parseInt(a, 2) ^ parseInt(b, 2)).toString(2);
 if(num.length !== a.length) {  // 因前面為0會被省略，所以要手動補0
    let padLength = a.length - num.length;
    num = "0".repeat(padLength) + num;
  };
  return num 
}

let key_ = "11110011"; // 密鑰

// 加密
(["01010100", "01100101", "01110011", "01110100"]).map(d => XOR(d, key_)); // 使用密鑰對每個元素做XOR

//加密後結果:  ["10100111", "10010110", "10000000", "10000111"]
```

然後進行解密

```js
//解密 (等於是把加密後的結果在與KEY做一次XOR)
(["10100111", "10010110", "10000000", "10000111"]).map(d => XOR(d, key_));

//解密後結果 ["01010100", "01100101", "01110011", "01110100"]

// 最後轉為字串
(["01010100", "01100101", "01110011", "01110100"]).map(d => String.fromCharCode(parseInt(d, 2))).join('');
// 得到字串:Test
```

# 簡化版為:

> 上面程式為展示二進位之XOR，我們也可以直接使用如下直接運算。

```js
var key = "test_key";
var password = "12345";
var encrypted = password ^ key;  
console.log(encrypted ^ key);
```

# Stream Ciphers 與 Block Ciphers

對稱式加密可以使用Stream Ciphers或是Block Ciphers來對明文進行加密。

> 可以在Node.js使用  crypto.getCiphers\(\) 來查看可用之加密方法。

#### 1.Stream Ciphers

Stream Ciphers\(資料流加密\)並不會將明文切分為區段，而是一次加密資料流的一個位元或是位元組。常見的作法是將 較短的加密鑰匙延展成為無限長、近似亂碼的一長串金鑰串流\(keystream\)，再將金鑰串流和原始資料 （plain text）經過XOR運算後，產生密文資料 （cipher text）。

但Stream Ciphers一般較難被實作，其較適合用於network streams串流方面。

#### 2.Block Ciphers

Block Ciphers\(區段加密\) 會將明文分成數個n個字元或位元的區段，並且對每 一個區段資料應用相同的演算法則和鑰匙。

假設M為明文，其將分割成M1、M2… Mn區段\)  然後K為密鑰，其可表示為: E\(M,K\)=E\(M1,K\)E\(M2,K\)… ..E\(Mn,K\)

較適合用於已知要加密的檔案內容。

> 參考至:[https://security.stackexchange.com/a/345](https://security.stackexchange.com/a/345)

以下先介紹比較早期發展之ECB，CBC，OFB和CFB

#### ECB

最簡單的加密模式即為（Electronic codebook，ECB）模式。需要加密的訊息按照塊密碼的大小被分為數個區塊，並對每個區塊進行獨立加密。但缺點在於同樣的明文區塊會被加密成相同的密文區塊，因此並不推薦用於密碼協定中。

> ![](/assets/er.png)[https://en.wikipedia.org/wiki/Block\_cipher\_mode\_of\_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)

#### CBC

全名為Cipher-block chaining，為區塊加密中最常用的模式，在CBC模式中，每個明文區塊先與前一個密文區塊進行XOR後，再進行加密，也就是將前一個區塊加密後的結果作為加密第二個區塊的輸入。在這種方法中，每個密文塊都依賴於它前面的所有明文區塊。並且，其會要求輸入一個初始向量，該初始向量會加入第一個明文區塊之加密。

主要缺點在於加密過程是依序進行的，無法同時進行，所以速度較慢，並且加密訊息必須被填充到區塊大小的整數倍。

> 其加密過程如下圖![](/assets/566.png)[https://en.wikipedia.org/wiki/Block\_cipher\_mode\_of\_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)

---

註1: \(兩兩數值相同為否，而數值不同時為真\): 例如1 xor 1為 0 ， 1 xor 0 為 1

F\(false\)亦代表0，T\(true\)亦代表1

![](/assets/2333.png)

至於為何要使用XOR在對稱式加密，不使用AND或OR，可參考:[https://stackoverflow.com/a/24731845](https://stackoverflow.com/a/24731845)
