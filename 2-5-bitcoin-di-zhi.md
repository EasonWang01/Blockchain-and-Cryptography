# 2-5 Bitcoin 地址

比特幣地址類似銀行帳號，可以公開告訴其他人，並且用其來進行收款動作

> 地址為1開頭為一般比特幣地址，為3開頭的為多重簽章地址

## 可在以下網站線上產生地址

單一簽名：[https://coinb.in/\#newAddress](https://coinb.in/#newAddress)

多重簽名：[https://coinb.in/multisig/](https://coinb.in/multisig/)

# 地址種類

```
1.Single Sig Address (Pay to Public Key Hash (P2PKH)) 單一簽名地址，開頭為1

2.Multi Sig Address （P2SH） 多重簽名地址，開頭為3

3.SegWit Address (P2WSH) 隔離見證地址，開頭為3

4.Time Locked Address 運用OP_CHECKLOCKTIMEVERIFY (OP_HODL)來創建的地址，開頭為3
```

下圖為比特幣地址的產生流程

![](/assets/address1.png)

> [http://en.bitcoinwiki.org/Bitcoin\_address](http://en.bitcoinwiki.org/Bitcoin_address)

比特幣地址有以下性質

> 1.由34個英文加數字組成  
> 2.不會含有大寫`O`大寫`I`小寫`i`和數字`0`

#### 使用Node.js產生比特幣地址

接下來我們要結合密碼學的幾個加密方法來實作並產生比特幣地址，使用的程式語言是Node.js\(如尚未安裝請參考附錄\)

