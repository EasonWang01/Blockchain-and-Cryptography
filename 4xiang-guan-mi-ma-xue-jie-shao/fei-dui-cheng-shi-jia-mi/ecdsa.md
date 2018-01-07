# ECDSA

有三個類似名詞為**ECC、ECDH、ECDSA，**第一個是Elliptic Curve Cryptography的縮寫，而後面兩個都是基於ECC的加密演算法

```
（1）相同密鑰長度下，安全性能更高，如160bits的ECC密鑰已經與1024bits之RSA、DSA有相同的安全強度。
（2）計算量小，處理速度快，在私鑰的處理速度上（解密和簽名），ECC比RSA、DSA快得多。
（3）存儲空間占用小，ECC的密鑰大小和系統參數與RSA、DSA相比要小得多，所以占用的存儲空間小得多。
```

> ECDSA和ECDH產生公私鑰的方式都相同

在數學上，橢圓曲線被定義為

$$y^2 = x^3 + ax + b$$

