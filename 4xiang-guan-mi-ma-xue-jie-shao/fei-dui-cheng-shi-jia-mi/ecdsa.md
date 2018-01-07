# ECDSA

有三個類似名詞為**ECC、ECDH、ECDSA，**第一個是Elliptic Curve Cryptography的縮寫，而後面兩個都是基於ECC的加密演算法

```
（1）相同密鑰長度下，安全性能更高，如160bits的ECC密鑰已經與1024bits之RSA、DSA有相同的安全強度。
（2）計算量小，處理速度快，在私鑰的處理速度上（解密和簽名），ECC比RSA、DSA快得多。
（3）存儲空間占用小，ECC的密鑰大小和系統參數與RSA、DSA相比要小得多，所以占用的存儲空間小得多。
```

> ECDSA和ECDH產生公私鑰的方式都相同

在數學上，橢圓曲線被定義為

$$y^2 = x^3 + ax + b$$ 並且 $$4a^3 + 27b^2 $$不為0

> 第二個條件是為了避免出現 \[singular curves\]\([https://en.wikipedia.org/wiki/Singularity\_\(mathematics\)\](https://en.wikipedia.org/wiki/Singularity_%28mathematics%29%29\)

可利用以下網站來模擬橢圓曲線，並調整參數

> [https://cdn.rawgit.com/andreacorbellini/ecc/920b29a/interactive/reals-add.html](https://cdn.rawgit.com/andreacorbellini/ecc/920b29a/interactive/reals-add.html![]%28/assets/螢幕快照)

![](/assets/螢幕快照 2018-01-07 下午5.44.12.png)可用OpenSSL指令，列出可用ECC曲線

```
openssl ecparam -list_curves
```

#### 求曲線上的點

假設，質數體為GF\(5\)且橢圓曲線公式 $$y^2 = x^3 + x +1$$

其上的點 x , y 為滿足$$y^2 $$ % 5 = \( $$ x^3 + x +1$$ \)% 5之值

> 這個橢圓曲線上的點，除無限遠點∞外， 另有8個點： \(0,1\), \(0,4\), \(2,1\), \(2,4\), \(3,1\), \(3,4\), \(4,3\), \(4,2\) 。 因為共有9點，所以此曲線的級數\(order\)為9。





