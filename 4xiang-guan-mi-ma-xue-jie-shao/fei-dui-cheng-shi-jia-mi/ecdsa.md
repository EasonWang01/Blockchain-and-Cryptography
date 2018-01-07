在介紹ECDSA之前我們先介紹三個類似名詞為**ECC、ECDH、ECDSA，**第一個是Elliptic Curve Cryptography的縮寫，而後面兩個都是基於ECC的加密演算法

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
>
> 如果橢圓曲線上的一個點P我們找到最小的正整數n 滿足n \* P = ∞ \(無限遠點\)，則n稱為點P的級數\(order\)。

# ECDSA

為DSA結合ECC橢圓曲線的簽名驗證演算法。ECDSA運作時會先把明文經過Hash，例如使用SHA系列，先對明文進行Hash。

而該Hash過的字串之二進位會再被切成長度與橢圓曲線級數之二進位相同長度字串。

#### 簽章步驟

1. 挑選一個隨機整數k

```
其中n - 1 ≥ k ≥ 1 ，n 為橢圓曲線的級數
```

1. 計算P

```
P = k * G(x,y)
G(x, y) 為之前挑選的基點(Base point)
```

1. 計算r

```
r 為 P 之 x 座標 mod n. 
> 如果算出來r為0，則回到第一步重新選一個k
```

4.

最後，計算 s ，如果算出s = 0，則回到第一步，換一個k重新計算。

$$s = k^-1 (h(M) + dr) $$ mod n

> 1. h\(M\)為剛才Hash過並切過\(**truncated**\)的訊息之二進位整數 2. d為私鑰
>    也就是計算： \(k的負一次方 mod n\)  \* \(\(h\(M\) + dr\) mod n\)

而產生出之簽章為 \(r, s\)

#### 驗證步驟：





