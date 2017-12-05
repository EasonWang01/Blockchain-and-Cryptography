The[`version`message](https://bitcoin.org/en/developer-reference#version)provides information about the transmitting[node](https://bitcoin.org/en/glossary/node)to the receiving[node](https://bitcoin.org/en/glossary/node)at the beginning of a connection. Until both[peers](https://bitcoin.org/en/glossary/node)have exchanged[`version`messages](https://bitcoin.org/en/developer-reference#version), no other messages will be accepted.

If a[`version`message](https://bitcoin.org/en/developer-reference#version)is accepted, the receiving[node](https://bitcoin.org/en/glossary/node)should send a[`verack`message](https://bitcoin.org/en/developer-reference#verack)—but no[node](https://bitcoin.org/en/glossary/node)should send a[`verack`message](https://bitcoin.org/en/developer-reference#verack)before initializing its half of the connection by first sending a[`version`message](https://bitcoin.org/en/developer-reference#version).

### 1.節點會先送出一個[`version`message](https://bitcoin.org/en/developer-reference#version) 給目標連線的節點，而該請求被另一個節點接收到並接收後會回覆[`verack`message](https://bitcoin.org/en/developer-reference#verack)

> version message發送，寫於原始碼，如下圖![](/assets/ˊ啊6876.png)https://github.com/bitcoin/bitcoin/blob/d3cb2b8acfce36d359262b4afd7e7235eff106b0/src/net.cpp\#L562



