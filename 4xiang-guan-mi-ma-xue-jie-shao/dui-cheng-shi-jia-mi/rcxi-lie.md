# RC系列

```
RC2 為64-bit區塊加密(Block cipher)在1987發表。

RC4 為目前最廣為使用的串流加密(stream cipher)。

RC5 為 32/64/128-bit 區塊加密(block cipher) 於 1994年開發。

RC6 為 128-bit 區塊加密(block cipher) 由 RC5 衍生而來。
```

#### RC2

**RC2  別名為 ARC2 由 **[Ron Rivest](https://en.wikipedia.org/wiki/Ron_Rivest)在1987年開發。 "RC" 意思為 "Ron's Code" 或是 "Rivest Cipher"

一開始他的演算法是保持保密的，直到1996年RC2 才被匿名的貼到[sci.crypt](https://en.wikipedia.org/wiki/Sci.crypt)論壇上。

1998年 Ron Rivest 才授權給[RFC](https://en.wikipedia.org/wiki/Request_for_Comments)公開。

RC2 is a[64-bit block](https://en.wikipedia.org/wiki/Block_size_%28cryptography%29)cipher with a variable size[key](https://en.wikipedia.org/wiki/Key_%28cryptography%29). Its 18 rounds are arranged as a source-heavy unbalanced[Feistel network](https://en.wikipedia.org/wiki/Feistel_network), with 16 rounds of one type \(MIXING\) punctuated by two rounds of another type \(MASHING\). A MIXING round consists of four applications of the MIX transformation, as shown in the diagram.

RC2 is vulnerable to a[related-key attack](https://en.wikipedia.org/wiki/Related-key_attack)using 234[chosen plaintexts](https://en.wikipedia.org/wiki/Chosen_plaintext)\(Kelsey et al., 1997\).



