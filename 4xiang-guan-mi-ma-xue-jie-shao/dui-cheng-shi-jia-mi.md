對稱式加密\(**Symmetric-key algorithm**\)

在加密和解密時使用相同的密鑰，常見的對稱是加密算法有：DES、AES、Serpent、Blowfish、TwoFish等等

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
  let num = (parseInt(a, 2) ^ parseInt(b, 2)).toString(2);
  if(num.length === 3) { // 因0在開頭會被省略
    num = "0" + num
  };
  return num 
}

let key_ = "11110011"; // 密鑰

(["01010100", "01100101", "01110011", "01110100"]).map(d => XOR(d, key_)); // 使用密鑰對每個元素做XOR
```

---

註1: \(兩兩數值相同為否，而數值不同時為真\): 例如1 xor 1為 0 ， 1 xor 0 為 1

F\(false\)亦代表0，T\(true\)亦代表1

![](/assets/2333.png)

