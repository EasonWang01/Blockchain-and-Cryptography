# Payload

此為訊息的主體部分

> 可參考比特幣開發者官網
>
> [https://bitcoin.org/en/developer-reference\#message-headers](https://bitcoin.org/en/developer-reference#data-messages)

訊息分為兩種類型：Data Messages 與 Control Messages

# 1.Data Messages

包含了所有與交易跟區塊有關的網路請求。

![](/assets/螢幕快照 2017-12-10 下午9.00.10.png)

# 2.Control Messages

![](/assets/螢幕快照 2017-12-10 下午9.00.17.png)

# 節點訊息傳送

1. version

.





.





.

---

> 我們看到的Hex 都屬於Little endian
>
> ```
> 如果有一個數字：70015 轉為16進位後為 1117F
>
> 這時因為兩個數字為一個bytes 可將其分為三個bytes: 01 11 7f
>
> 將這三個bytes前後順序(byte order)轉換
>
> 可得到 7f 11 01
> ```

![](/assets/螢幕快照 2017-12-15 下午4.16.07.png)

