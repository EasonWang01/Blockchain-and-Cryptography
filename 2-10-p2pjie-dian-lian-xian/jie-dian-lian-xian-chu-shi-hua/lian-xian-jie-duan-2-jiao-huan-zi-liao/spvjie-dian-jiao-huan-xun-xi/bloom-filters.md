# Bloom Filter

上一章節提到的SPV節點可以減少在節點上所需要下載的區塊鏈大小，而 Bitcoin在 BIP: 37 引入了Bloom Filter 機制，可以讓節點利用此機制發出過濾請求，得到想要的資訊。

> BIP: 37可參考如下連結：
>
> [https://github.com/bitcoin/bips/blob/master/bip-0037.mediawiki\#filter-matching-algorithm](https://github.com/bitcoin/bips/blob/master/bip-0037.mediawiki#filter-matching-algorithm)

### Bloom Filter 概念

Bloom Filter 很早之前即開始使用在各種場景，1970年由 『  Burton Howard Bloom 』提出。

當我們想判斷一個元素是不是在一個集合裡時，可以通過Array、Tree、Hash Table等資料結構實現，但他們的時間複雜度分別為  
 O\(n\) 、  O\(log n\) 與  O\(n/k\)，且在效率最好的Hash Table中又必須儲存鍵值\(Key與Value\)，而Bloom Filter不需要在資料中多存鍵值並且其時間複雜度可以達到O\(k\)。

### Bloom Filter 實現原理

> ![](/assets/螢幕快照 2017-12-27 下午9.16.png)https://en.wikipedia.org/wiki/Bloom\_filter





---

## False Positive 與 False Nagative

False Positive:  答案中表示該處是有資料的，但其實該處沒有資料

False Nagative: 答案中表示該處沒有資料，但其實該處有資料

