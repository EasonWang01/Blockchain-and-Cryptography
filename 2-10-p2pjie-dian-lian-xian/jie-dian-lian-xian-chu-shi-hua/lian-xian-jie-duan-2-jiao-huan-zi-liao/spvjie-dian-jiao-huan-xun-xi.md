# SPV節點

比特幣之區塊鏈大小較為龐大，如果下載所有資料後需要佔據大約 100GB 以上的硬碟儲存空間，所以後來發展出了一種方法，可以不用下載整個區塊鏈的節點，稱為 SPV節點 \( Simplified Payments Verification nodes \)。

上一章節的節點資料交換為Full Node \(全節點\) 的資料交換方式，而這章節將講解可以有效節省空間的SPV Node，SPV Node只會下載區塊頭（ Block Header） 不需要像 Full node 一樣去下載每個區塊裡面的所有 transactions。

SPV節點使用Merkle Root驗證特定交易是否存在於區塊中，不用管其他不相關的交易，而在安全性考量下，SPV節點通常會有許多個連線的Full Node來確保傳過來的資料是正確的。

在SPV節點中不用像上一章節發出 getdata 來請求資料，而是發出 getheader 請求，之後當另外一個節點接收到後會回覆 header 訊息。

![](/assets/螢幕快照 2017-12-26 下午5.16.09.png)

## getheader

將先前程式的 Buffer 部分改為如下再次執行，即可模擬 :

```js
 const magicNum = "f9beb4d9";
 const command = "676574686561646572730000" // getheaders
 const payload_length = "e5030000"
 const checksum = "c03eac5d"

  // 7f110100為版本號
  // 1e 為發出的header hash數量
  // 之後 30 行的 hash 為 Block Header hash
  // 最後面的00....為End結束的意思
 const payload = `7f110100  
                    1e
                    a5e9078c58ff2b934450022350ede0cec6ef111aca9014000000000000000000
                    fae4a61a5d5ba145db9187c9b3fc931f1e91153dbc6e07000000000000000000
                    9a6fcde0669aadadc6d60501c7354d7b3d83cee9e17690000000000000000000
                    72c62b9147c7e55f75aaf93ecf4feef07e0d8beaf63608000000000000000000
                    29dc86cd0fc41973c6372ba277e65e97c90697f2006449000000000000000000
                    c6887f81c2760ad8673eeb3894b7c2a2d7d3baf5014c6e000000000000000000
                    dd72b15750112be686899bbc20cbe9772aefb3531a8d52000000000000000000
                    97362eb4baa07fe321f5e162743039535de92ec1ddd23a000000000000000000
                    fb82f36cfcd1dbc7adce764fd1174be8e9ff3e880a8d71000000000000000000
                    7dee2ee535ffddbb2830d97c42fcdc074e10bf57f33961000000000000000000
                    632659230890db0c35d5fef33c1d1a332b92466e15ae3e000000000000000000
                    9eb910cb5c5c0814aa99156b6b50bf1e5ddccb2a631938000000000000000000
                    c3eb5c52ee1e8dfe59ccdff3c76992876461257158d448000000000000000000
                    9bd147dc938e4d46b3e5fe7627de74274c0c2125744a4f000000000000000000
                    daf34b0f0364b9edb7996f0bfeb8d0baf70825771d5487000000000000000000
                    848f1d420987d8a58598ac7054699e65ff6cbd9eee1f1a000000000000000000
                    2f7a8d7cdeeb6125b42dab1f5f70cfe95922f6fc730557000000000000000000
                    da56b083e6995fb4f6bbbc56a43f181215b7f35357cb0f000000000000000000
                    dbc56f111f8a0fd7bc6c69187807ae3328cdb50008721f000000000000000000
                    f57ddcba4e1caf8b78429fa99594c15268d8c4f71cd56d000000000000000000
                    bd18aab88bf9fbef1131ff559f061b8f5feda5f1db7925000000000000000000
                    0d6fafe3918465c62b768f6b682f74b48ff549245b950b000000000000000000
                    d06d9bd52d767010fb2fb495e43af827dfa04017a4b23a000000000000000000
                    5317e5ad4a1228f78c8f8a33a8b3fa29fa07b409f5a0a4000000000000000000
                    035769f494f7be3f54eb11f0e619037fa42db67a34d00d000000000000000000
                    3ff2a3c3af68abc62766bb219ec50262c2ffeef07c2c9e010000000000000000
                    ff8001903f40ebbd03be7107031fa881fe0f2e3094b602040000000000000000
                    f04a351481138689deb7b3ee053c508d9e649370626811090000000000000000
                    06b8cb9aa4bb01728891862904d5fc32e04b62f53c9fd47ded00000000000000
                    6fe28c0ab6f1b372c1a6a246ae63f74f931e8365e15a089c68d6190000000000
                    0000000000000000000000000000000000000000000000000000000000000000`;

const buffer = new Buffer(magicNum + command + payload_length + checksum + payload.replace(/\s/g,''), 'hex');
```

## headers

收到getheader後的節點，將回覆 headers 訊息，與blocks訊息類似，也會將一開始傳過來的訊息比對，將對方沒有的部分傳送給對方。

![](/assets/螢幕快照 2017-12-26 下午7.17.14.png)

將先前程式的 Buffer 部分改為如下再次執行，即可模擬 :

```js
const magicNum = "f9beb4d9";
const command = "686561646572730000000000" // headers
const payload_length = "52000000"
const checksum = "ef8dc6fd"

const count = '0100' // 包含hash總數

/* 區塊頭 */
const block_version = '000020'
const previous_block = '91c7d27b24f9a5a6042adeadb33ccaa1af671b26ccb235000000000000000000'
const merkle_root = '67dce235f09410f8bcb0f4b0b894db5a8e66adeb58de59b01978df3a92581682'
const block_timestamp = '3e99fb59' // Nov  3, 2017 06:16:30.000000000 +08
const bits = '45960018';
const nonce = 'c43de516';
const transaction_count= '00';
const payload = count + block_version + previous_block + merkle_root + block_timestamp + bits + nonce + transaction_count;

const buffer = new Buffer(magicNum + command + payload_length + checksum + payload, 'hex');
```



