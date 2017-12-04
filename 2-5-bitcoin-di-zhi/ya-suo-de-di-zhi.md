# 壓縮的地址

其實應該是說壓縮後的public key產生之地址，因為橢圓曲線公式知道x後即可求得y，所以沒有必要把公鑰的x和y都加上去

未壓縮

```
04 + <x座標> + <y座標>
```

壓縮

```
02 or 03 + <x座標>
```

> Uncompressed [public keys](https://bitcoin.org/en/glossary/public-key) start with 0x04;

> [Compressed public keys](https://bitcoin.org/en/glossary/compressed-public-key) begin with 0x03 or 0x02 depending on whether they’re greater or less than the midpoint of the curve. These prefix bytes are all used in official [secp256k1](http://www.secg.org/sec2-v2.pdf) documentation.



