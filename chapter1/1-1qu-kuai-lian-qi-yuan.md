# 1-1 區塊鏈起源

#### **1982年**

Leslie Lamport等人提出拜占庭將軍問題（Byzantine Generals Problem）

其主要概念為：拜占庭帝國軍隊的將軍們必須全體一致的決定是否攻擊某一支敵軍。問題是這些將軍在地理上是分隔開來的，各個將軍必須想辦法讓各地軍隊彼此取得共識、並且決定是否進行出兵，最後將此取得共識的過程延伸至運算領域，設法建立具容錯性的分散式系統，即使部分節點失效仍可確保系統正常運行，可讓多個基於零信任基礎的節點達成共識，並確保資訊傳遞的一致性。

在區塊鏈上節點間的同步即要解決相關一致性問題。

![](/assets/拜占庭將軍論文封面.png)

#### 1982-1983年

David Chaum提出Ecash密碼學網路支付系統並基於非對稱式加密法 RSA 與 DSA 提出了 Blind signature，具有不可追蹤的特性。

#### 1989年

David Chaum創立了DigiCash Inc.

提供匿名的電子支付服務，但沒有獲得許多的使用者。

#### 1990年

Leslie Lamport提出具高容錯的一致性演算法：Paxos。

Paxos算法用來解決分散式系統中，各個節點如何就某個特定值達成一致，保證不論發生任何異常，都不會破壞決議的一致性，主要做法是進行提案與表決。

[https://zh.wikipedia.org/wiki/Paxos算法](https://zh.wikipedia.org/wiki/Paxos算法)

#### 1992年

Scott Vanstone提出橢圓曲線數位簽章演算法（Elliptic Curve Digital Signature Algorithm，ECDSA）

> ECDSA為結合ECC曲線和DSA ; ECC 為1985年由Neal Koblitz與 Victor S. Miller提出

利用橢圓曲線的離散特性使他的密碼安全性比先前產生密鑰的方式更加安全，並且被廣泛採用在現今的加密系統中

> ECC: [https://en.wikipedia.org/wiki/Elliptic-curve\_cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography)
>
> ECDSA: [https://en.wikipedia.org/wiki/Elliptic\_Curve\_Digital\_Signature\_Algorithm](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)

#### 1997年

Adam Back發明Hashcash（雜湊現金），為一種工作量證明演算法（Proof of Work，POW），由於相關雜湊演算法的不可逆特性，達到容易被驗證，但很難被破解的特性， 最早被應用於阻擋垃圾郵件。Hashcash之後成為比特幣區塊鏈所採用的關鍵技術之一。

Adam Back於2002年正式發表Hashcash論文

> [http://www.hashcash.org/papers/hashcash.pdf](http://www.hashcash.org/papers/hashcash.pdf)

#### **1998年**

Wei Dai發表匿名的分散式電子現金系統B-money，引入工作量證明機制，強調點對點交易和不可竄改特性。不過在B-money中，並未採用Adam Back提出的Hashcash演算法。Wei Dai的許多設計之後被比特幣區塊鏈所採用。

[https://en.bitcoin.it/wiki/B-money](https://en.bitcoin.it/wiki/B-money)

同年，NickSzabo發表了Bit Gold，參與者可貢獻運算能力來解出加密謎題。

[http://nakamotoinstitute.org/bit-gold/](http://nakamotoinstitute.org/bit-gold/)

#### **2005年**

Hal Finney依據Nick Szabo 的 `theory of collectibles`提出可重複使用的工作量證明機制（Reusable Proofs of Work，RPOW），結合B-money與Adam Back提出的Hashcash演算法來創造密碼學貨幣，可視為加密貨幣的前輩。

[http://nakamotoinstitute.org/finney/rpow/](http://nakamotoinstitute.org/finney/rpow/)

[https://en.bitcoin.it/wiki/Hal\_Finney](https://en.bitcoin.it/wiki/Hal_Finney)

> 我們可在此[https://github.com/NakamotoInstitute/RPOW](https://github.com/NakamotoInstitute/RPOW) 看到RPOW的原始碼

#### 2008年10月31日

中本聰發布了白皮書：《Bitcoin：A Peer-to-Peer Electronic Cash System》

![](/assets/比特幣白皮書封面1-1.png)

[https://bitcoin.org/bitcoin.pdf](https://bitcoin.org/bitcoin.pdf)

#### 2009年1月3日18∶15∶05

中本聰在位於芬蘭赫爾辛基（Helsinki）的一臺電腦上產生了比特幣第一個區塊（Genesis Block），並在區塊訊息寫下當天《泰晤士報》的頭版標題：`The Times 03/Jan/2009 Chancellor on brink of second bailout for banks`。

![](/assets/Jonny1000thetimes.png)

> 《泰晤士報》2009/1/3的頭版標題 [https://en.bitcoin.it/wiki/File:Jonny1000thetimes.png](https://en.bitcoin.it/wiki/File:Jonny1000thetimes.png)

十六進位表示的Genesis block![](/assets/genesis_block_raw.png)

現在仍可查到當時創世區塊的資訊

![](/assets/創世區塊資訊.png)

> [https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f](https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f)

#### 2010年5月22日

一名佛羅里達工程師Laszlo Hanyecz在2010/5/17刊登出想要用比特幣購買披薩，之後，在同年5/22號，成功用10,000 BTC購買了價值25美元的披薩。

#### 2011年2月9日

比特幣每顆價格首次價格超越1美元![](/assets/首次超越1.png)

#### 2013年10月

世界第一台比特幣櫃員機出現在加拿大的溫哥華。

#### 2013年末

一名19歲少年Vitalik Buterin，發表了Ethereum 的 white-paper。

[https://github.com/ethereum/wiki/wiki/White-Paper](https://github.com/ethereum/wiki/wiki/White-Paper)

#### 2014年1月

Vitalik在美國佛羅裏達州邁阿密舉行的北美比特幣會議上正式宣布了Ethereum這個Project。

#### 2014年4月

與Vitalik 合作的 Gavin Wood發表了以太坊黃皮書，作為Ethereum虛擬機的技術說明。

#### 2014年6月

Ethereum以42天公開的Ethereum預售活動對第一批以太幣進行了分配，換算後大約收入31,591比特幣，當時價值 18,439,086 美元，售出共60,102,216以太幣。

### 近期

隨著比特幣與以太幣發行後，越來越多的加密貨幣也跟著發行並且募資，也衍伸了後來 ICO \(Initial Coin Offering\) 名詞的誕生。

---

參考資料 :

[https://en.bitcoin.it/wiki/Bitcoin\_Firsts](https://en.bitcoin.it/wiki/Bitcoin_Firsts)

[https://github.com/ethereum/wiki/wiki/White-Paper](https://github.com/ethereum/wiki/wiki/White-Paper)

