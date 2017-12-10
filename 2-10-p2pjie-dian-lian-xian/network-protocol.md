# 比特幣網路Protocol

> ![](/assets/螢幕快照 2017-12-06 下午9.59.32.png)[https://en.bitcoin.it/wiki/Protocol\_documentation\#Message\_structure](https://en.bitcoin.it/wiki/Protocol_documentation#Message_structure)

---

> 分為兩個部分一個為message header包含四個欄位，另一個為payload

# Message Header

### 1. magic

參考下圖，此為比特幣預設定義好的數字

![](/assets/螢幕快照 2017-12-10 下午8.42.29.png)

### 2. command

用來區分是執行哪一種類型的message

例如version、getaddr、getdata等 （將於下面payload章節部分講解有關所有message的種類）

會轉為ascii十六進位，然後不滿12個字的話會在後面補0

EX: getdata

```
67 65 74 64 61 74 61 00 00 00 00 00
```

### 3. length

代表payload中有多少個bytes

> 十六進制hex每個字為4 bits也就是0.5bytes，每個ascii為兩個hex碼組成，所以bytes總數及為 `hex碼之數量 / 2`

假設今天payload message如下，總共204個字，則length及為102

```
7f1101000d0000000000000060ee275a000000000d0000000000000000000000000000000000ffff9294741e208d0d0000000000000000000000000000000000000000000000000038198a1d11f35244102f5361746f7368693a302e31332e322f8000000001
```

### 4. checksum

> First 4 bytes of sha256\(sha256\(payload\)\)

會把payload 進行Double sha256後，取前四個bytes

---

# 



