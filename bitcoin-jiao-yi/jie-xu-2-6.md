# 其他類型交易

#### 1.Data Output \( OP\_RETURN，可以填上自己想填的資料到交易上 \)

為了可以在比特幣交易中加上額外的自訂訊息，後來發展出此種做法，最多可以帶 83 bytes 的資料 \( Bitcoin Core 0.12.0\)。

![](/assets/螢幕快照 2018-02-01 下午3.04.42.png)[https://bitcoin.org/en/developer-guide\#null-data](https://bitcoin.org/en/developer-guide#null-data)

OP\_RETURN 沒有 Unlocking script，並且在 isStandard\(\) 會被判斷為 invalid，一個交易的 Outputs 只能有一個 OP\_RETURN，而OP\_RETURN 花費進去的比特幣是無法轉出的。

[https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/script/standard.h\#L34](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/script/standard.h#L34)

```
OP_RETURN <data>
```

實際交易

> ![](/assets/螢幕快照 2018-02-01 下午3.17.04.png)
>
> [https://live.blockcypher.com/btc-testnet/tx/25cc262f03ba87f3241894438837f3e805e8954538e3fa45d31f2f82fd3119a3/](https://live.blockcypher.com/btc-testnet/tx/25cc262f03ba87f3241894438837f3e805e8954538e3fa45d31f2f82fd3119a3/)

接著我們使用線上的服務來查看OP\_RETURN 的交易內容：

> [https://api.blockcypher.com/v1/btc/test3/txs/25cc262f03ba87f3241894438837f3e805e8954538e3fa45d31f2f82fd3119a3?limit=50&includeHex=true](https://api.blockcypher.com/v1/btc/test3/txs/25cc262f03ba87f3241894438837f3e805e8954538e3fa45d31f2f82fd3119a3?limit=50&includeHex=true)

進入後可以往下看到：

![](/assets/kasd423423.png)

最後我們用以下程式，把該字串從 Hex 轉為 ASCII 即可看到原始內容：

```js
function hex_to_ASCII(hexx) {
  let hex = hexx.toString();
  let str = '';
  for (let i = 0; i < hex.length; i += 2)
    str += String.fromCharCode(parseInt(hex.substr(i, 2), 16));
  return str;
}

hex_to_ASCII('4120636f6e74656e746564206d696e64206973206120636f6e74696e75616c2066656173742e');
```

#### 2.Pay-to-Script-Hash \(P2SH\)

為一開始發展之多重簽名 BIP-11 的衍伸，其定義在 BIP-16，因為在傳統的 MultiSig 需要放入多個 Public Key，會造成 Script 過長，所以後來決定把具有多個 Public Key 之 Locking Script 做雜湊，產生一個 20-byte 的 Redeem Script。

**2 to 5 之 P2SH script：**

```go
Redeem script: 
2 <Public Key A> <Public Key B> <Public Key C> <Public Key D> <Public Key E> 5 OP_CHECKMULTISIG

Locking script:
OP_HASH160 <20-bytes Redeem script> OP_EQUAL

Unlocking script:
<Public Key B> <Public Key C>  <20-bytes Redeem script>
```

可參考: [https://bitcoin.stackexchange.com/a/28092](https://bitcoin.stackexchange.com/a/28092)

#### 3.Segregated Witness \( Segwit \)

中文為隔離見證，主要用意是要減少放入區塊內的交易大小，作法為把原本交易簽名（signature）從交易 Script 中移除。

與Segwit相關的 BIP 包含 [BIP141](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki)、[BIP143](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki)、[BIP144](https://github.com/bitcoin/bips/blob/master/bip-0144.mediawiki)、[BIP145](https://github.com/bitcoin/bips/blob/master/bip-0145.mediawiki)等等。

每個Segwit的交易將會產生兩種ID : txid與wtxid。

```
txid:   [nVersion][txins][txouts][nLockTime]
wtxid:  [nVersion][marker][flag][txins][txouts][witness][nLockTime]
```

> 其中`nVersion、txins、txouts、nLockTime`四個部分類似於其他類型的交易
>
> marker目前為`0x00`
>
> flag目前為`0x01`
>
> witness為交易的txins之簽名

之後一群交易的 wtxid 會另外組成一個 Merkle Tree，並且將 Merkle Root 存在區塊的 coinbase transaction。

分為以下兩種：Pay-to-Witness-Public-Key-Hash \( P2WPKH \) 與 Pay-to-Witness-Script-Hash \( P2WSH \)

#### P2WPKH

> 版本號為 0 之 pay-to-witness-public-key-hash \( P2WPKH \)

```
witness:      <signature> <pubkey>
scriptSig:    (empty)
scriptPubKey: 0 <20-byte-key-hash>
```

交易範例：[http://n.bitcoin.ninja/checktx?txid=d869f854e1f8788bcff294cc83b280942a8c728de71eb709a2c29d10bfe21b7c](http://n.bitcoin.ninja/checktx?txid=d869f854e1f8788bcff294cc83b280942a8c728de71eb709a2c29d10bfe21b7c)

![](/assets/螢幕快照 2018-01-20 下午10.59.02.png)

#### P2WSH

> 版本號為0 之 1-of-2 multi-signature  pay-to-witness-script-hash \(P2WSH\)

```
witness:      0 <signature1> <1 <pubkey1> <pubkey2> 2 CHECKMULTISIG>
scriptSig:    (empty)
scriptPubKey: 0 <32-byte-hash>
```

交易範例：[http://n.bitcoin.ninja/checktx?txid=78457666f82c28aa37b74b506745a7c7684dc7842a52a457b09f09446721e11c](http://n.bitcoin.ninja/checktx?txid=78457666f82c28aa37b74b506745a7c7684dc7842a52a457b09f09446721e11c)

![](/assets/螢幕快照 2018-01-20 下午11.03.32.png)

其他有關Segwit之BIP可參考：

```
BIP141, BIP143, BIP144, BIP145
```

---

註1 : lock\_time   [https://en.bitcoin.it/wiki/Timelock](https://en.bitcoin.it/wiki/Timelock)  
以Unix timestamp表示，類似於`1511321691`如果填入該欄位的值小於五億，則會把該數字視為區塊高度，意思為在該區塊高度之前不能將交易加入區塊

![](/assets/09isd.png)

> [https://en.bitcoin.it/wiki/Protocol\_documentation\#tx](https://en.bitcoin.it/wiki/Protocol_documentation#tx)

註2 : 交易手續費，以交易大小來計算，每 KB 約為 1000 satoshis  
，每個交易通常至少含有500 bytes。

> [https://bitcoinfees.earn.com/](https://bitcoinfees.earn.com/)  
>  \(此網站可看到目前推薦的手續費與尚未確認的交易所含的手續費\)  
>  注意 : 網站上是以 bytes 為單位

其他參考資料：  
[https://github.com/OutCast3k/coinbin/blob/217897285e51cbc33bdba3ec275aa3386ebf70b2/js/coin.js\#L793](https://github.com/OutCast3k/coinbin/blob/217897285e51cbc33bdba3ec275aa3386ebf70b2/js/coin.js#L793)  
[https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction.js](https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction.js)  
[https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction\_builder.js](https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction_builder.js)

