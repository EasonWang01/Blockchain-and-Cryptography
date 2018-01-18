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

假設公鑰





