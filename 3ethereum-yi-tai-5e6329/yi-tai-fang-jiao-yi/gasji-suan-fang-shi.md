# Gas 計算方式

在一開始時我們提到過交易手續費是使用 Gas 來計算：

```
交易手續費 Tx Fees = Gas Limit * Gas Price
```

其中 Gas Limit 相當於最多允許用多少單位個Gas來執行這個交易或是合約，Gas Price 為每單位 Gas 的價格。

接下來我們可以去查看 Ethereum 黃皮書上的交易手續費表

> 在第24頁，附錄Appendix G. Fee Schedule
>
> [https://ethereum.github.io/yellowpaper/paper.pdf](https://ethereum.github.io/yellowpaper/paper.pdf)

![](/assets/螢幕快照 2018-01-29 下午5.18.54.png)

我們以Remix IDE來看，在執行合約時填入的 Gas Limit 為允許最多消耗多少Gas，如果執行合約需要比這個更多的Gas則執行不會成功，用來預防執行時耗費太多Gas，如果執行合約的 Gas 小於 Gas Limit 則會把多的退還。

下面的Value不是設定Gas Price，而是代表要發送多少以太幣到合約，通常用來設定發送到合約中 Payable Function 的金額。

![](/assets/螢幕快照 2018-01-29 下午8.23.00s.png)

## 交易Input

接著我們執行一筆交易，然後在Remix IDE console的地方點選該筆執行的Details按鈕，可以看到如下

![](/assets/螢幕快照 2018-01-29 下午8.47.39.png)

接著我們用SHA3把我們執行的Function名稱：`teacher()` 做雜湊然後取前八個字，即可看到出現跟交易Input相同的字串。

![](/assets/螢幕快照 2018-01-29 下午8.49.04.png)

再來我們看到如果是Function具有多個參數的情況：

![](/assets/螢幕快照 2018-01-29 下午8.56.28.png)

我們一樣把Function名稱做SHA3 Hash

![](/assets/螢幕快照 2018-01-29 下午8.57.31.png)

接著看到在Input欄位，除了開頭的Hash外後面還多了很多0，那是我們Function的參數，其中參數型態為 uint256 會佔有 32 bytes，所以是 64 個 Hex 字。可以看到上面表格中的Input欄位中，可以看到在許多0後面接著一個`c` ，而`c`即為我們輸入的參數12的十六進位表示，在Input的倒數第二行所看到的`0474657374` 轉為ASCII string 後即為我們所輸入的第一個參數 :`test`

## 計算交易費用

我們如果把Remix IDE切換成用VM模式執行，則可以看到多出一個名為execution cost欄位

![](/assets/螢幕快照 2018-01-29 下午9.23.39.png)我們先將兩個值相減

```
124948 - 102332 = 22616
```

然後計算Input的花費：

1.我們先計算Input共多少個bytes

![](/assets/螢幕快照 2018-01-29 下午9.33.04.png)

> 共392個字，每個Hex為0.5 bytes，所以總共為196 bytes。

2.計算幾個zero byte與non-zero bytes

兩個兩個一組來看，總共出有13個non-zero bytes，所以我們196 - 13 = 183。

> 每個zero byte 需要花費 4 gas
>
> 每個non-zero bytes 需要花費 68 gas

```
(13 * 68) + ( 183 * 4) + 21000 = 22616
```

可以發現與上面得到相同答案`transaction cost - execution cost`。

> 在Remix IDE 中顯示的 transaction cost 意思比較類似於 Total cost，也就是我們原本理解的`transaction cost + execution cost`

## Execution cost

接著我們點選Debug按鈕，然後點選右側上方的Instructions。

> 必須要在Javascript VM環境下執行才可使用
>
> 或是Geth 要開啟debug的RPC `--rpcapi "eth,net,web3,debug"`

![](/assets/螢幕快照 2018-01-29 下午9.40.44.png)我們看到的Instruction為Ethereum Assembly instructions，計算execution cost即從此過程計算。

接著我們比對黃皮書

![](/assets/螢幕快照 2018-01-29 下午9.57.23.png)可以看到每個種類分別的Gas花費

![](/assets/螢幕快照 2018-01-29 下午9.57.53.png)這樣我們即可算出相關的execution cost。

最後，我們可以點選Step detail，看到我們這筆交易所剩餘的Gas。

![](/assets/螢幕快照 2018-01-29 下午10.04.13.png)

參考資料：

[https://vomtom.at/what-exactly-is-the-gas-limit-and-the-gas-price-in-ethereum/](https://www.gitbook.com/book/easonwang01/e/edit#)

