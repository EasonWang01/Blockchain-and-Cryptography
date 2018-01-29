[https://vomtom.at/what-exactly-is-the-gas-limit-and-the-gas-price-in-ethereum/](https://vomtom.at/what-exactly-is-the-gas-limit-and-the-gas-price-in-ethereum/)

# Gas 計算方式

在一開始時我們提到過交易手續費是使用Gas來計算。

```
交易手續費 Tx Fees = Gas Limit * Gas Price
```

其中Gas Limit相當於最多允許用多少單位個Gas來執行這個交易或是合約，Gas Price為每單位Gas的價格。

接下來我們可以去查看Ethereum黃皮書上的交易手續費表

> 在第24頁，附錄Appendix G. Fee Schedule
>
> [https://ethereum.github.io/yellowpaper/paper.pdf](https://ethereum.github.io/yellowpaper/paper.pdf)

![](/assets/螢幕快照 2018-01-29 下午5.18.54.png)

我們以Remix IDE來看，在執行合約時填入的Gas Limit為允許最多消耗多少Gas，如果執行合約需要比這個更多的Gas則執行不會成功，用來預防執行時耗費太多Gas，如果執行合約的Gas小於Gas Limit則會把多的退還。

下面的Value不是設定Gas Price，指的是要發送多少以太幣到合約，通常用來設定發送到Payable Function的金額。

![](/assets/螢幕快照 2018-01-29 下午8.23.00s.png)

接著我們執行一筆交易，然後在Remix IDE console的地方點選該筆執行的Details按鈕，可以看到如下

![](/assets/螢幕快照 2018-01-29 下午8.47.39.png)

接著我們用SHA3把我們執行的Function名稱：`teacher()` 做雜湊然後取前八個字，即可看到出現跟交易Input相同的字串。

![](/assets/螢幕快照 2018-01-29 下午8.49.04.png)

再來我們看到如果是多個參數的話

![](/assets/螢幕快照 2018-01-29 下午8.56.28.png)

我們一樣把Function名稱做SHA3 Hash

![](/assets/螢幕快照 2018-01-29 下午8.57.31.png)

接著看到在Input欄位，除了開頭的Hash外後面還多了很多0，那是我們Function的參數，其中uint256會佔有32bytes，所以是64個Hex字。可以看到`c` 即為我們輸入的參數12，`0474657374` 轉為ASCII string 後即為我們所輸入的`test`

