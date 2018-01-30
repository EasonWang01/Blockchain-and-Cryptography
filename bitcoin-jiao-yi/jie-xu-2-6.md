#### 

其解鎖Unlocking script如下

```
OP_0 <Signature B> <Signature C>
```

兩者結合

```
Pubkey script: <m> <A pubkey> [B pubkey] [C pubkey...] <n> OP_CHECKMULTISIG
Signature script: OP_0 <A sig> [B sig] [C sig...]
```

#### 4.Data Output \(OP\_RETURN，可以填上自己想填的資料到交易上\)

有些人會想要在區塊鏈上加上客製化的資料，所以後來發展出此種做法，最多可以帶83bytes的資料\(Bitcoin Core 0.12.0\)，OP\_RETURN 沒有 Unlocking script，並且在 isStandard\(\) 會被判斷為 invalid，一個交易的 Outputs 只能有一個 OP\_RETURN，而OP\_RETURN 花費進去的比特幣是無法轉出的。

[https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/script/standard.h\#L34](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/script/standard.h#L34)

```
OP_RETURN <data>
```

實際交易

> ![](/assets/987.png)[https://blockchain.info/tx/8bae12b5f4c088d940733dcd1455efc6a3a69cf9340e17a981286d3778615684](https://blockchain.info/tx/8bae12b5f4c088d940733dcd1455efc6a3a69cf9340e17a981286d3778615684)

接著我們使用線上的服務來查看OP\_RETURN 的TX內容： [https://blockchain.info/rawtx/8bae12b5f4c088d940733dcd1455efc6a3a69cf9340e17a981286d3778615684](https://blockchain.info/rawtx/8bae12b5f4c088d940733dcd1455efc6a3a69cf9340e17a981286d3778615684)

進入後可以往下看到：

![](/assets/螢幕快照 2018-01-21 上午9.45.10.png)

之後我們使用此網站來解碼 script 內容 :

[https://chainquery.com/bitcoin-api/decodescript](https://chainquery.com/bitcoin-api/decodescript)

![](/assets/螢幕快照 2018-01-21 上午9.43.57.png)

> 可以看到OP\_RETURN 後面接著一串十六進位字串

最後我們把該字串從Hex轉為ASCII即可看到內容

```js
function hex_to_ASCII(hexx) {
    let hex = hexx.toString();
    let str = '';
    for (let i = 0; i < hex.length; i += 2)
        str += String.fromCharCode(parseInt(hex.substr(i, 2), 16));
    return str;
}

hex_to_ASCII('636861726c6579206c6f766573206865696469');
```

#### 5.Pay-to-Script-Hash \(P2SH\)

為一開始發展之多重簽名BIP-11的衍伸，其定義在BIP-16，因為在傳統的Multi Sig需要放入多個Public Key，所以後來決定把具有多個Public Key之script做雜湊，產生一個20-byte 的 Redeem Script。

附帶以下優點

```
1. 複雜的 locking script 變成只有 20 bytes 的 digital fingerprint。
2. Script Hash 可以 encode 成 address，讓傳送者可以不需要有複雜的軟體才能使用。
3. P2SH 把儲存容量的負擔從 UTXO set 轉移到 Blockchain 上面，因為在 Tx 的 outputs 裡面記錄的 locking script 變短了。
4. P2SH 把資料儲存的負擔從當下移到未來，只有在再次花費該筆金額時。
5. P2SH 把手續費的負擔轉移到接收者身上。
```

```
2 to 5 之 P2SH

Redeem script: 
2 <Public Key A> <Public Key B> <Public Key C> <Public Key D> <Public Key E> 5 OP_CHECKMULTISIG

Locking script:
OP_HASH160 <20-bytes Redeem script> OP_EQUAL

Unlocking script:
<Public Key B> <Public Key C>  <20-bytes Redeem script>
```

可參考: [https://bitcoin.stackexchange.com/a/28092](https://bitcoin.stackexchange.com/a/28092)

#### 6.Segregated Witness \( Segwit \)

[https://github.com/bitcoin/bips/blob/master/bip-0142.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0142.mediawiki)

中文為隔離見證，主要用意是要減少放入區塊內的交易大小，作法為把原本交易簽名（signature）從交易Script中移除。

每個Segwit的交易將會產生兩種ID : txid與wtxid。

```
txid:   [nVersion][txins][txouts][nLockTime]
wtxid:  [nVersion][marker][flag][txins][txouts][witness][nLockTime]
```

> 其中`nVersion、txins、txouts、nLockTime`四個部分是相同的
>
> marker目前為`0x00`
>
> flag目前為`0x01`
>
> witness為交易的txins之簽名

之後一群交易的wtxid會另外組成一個Merkle Tree，並且將Merkle Root存在區塊的coinbase transaction。

#### P2WPKH

> 版本號為 0 之 pay-to-witness-public-key-hash \(P2WPKH\)

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

.交易範例：[http://n.bitcoin.ninja/checktx?txid=78457666f82c28aa37b74b506745a7c7684dc7842a52a457b09f09446721e11c](http://n.bitcoin.ninja/checktx?txid=78457666f82c28aa37b74b506745a7c7684dc7842a52a457b09f09446721e11c)

![](/assets/螢幕快照 2018-01-20 下午11.03.32.png)

其他有關Segwit之BIP可參考：

```
BIP141, BIP143, BIP144, BIP145
```

---

註1: lock\_time   [https://en.bitcoin.it/wiki/Timelock](https://en.bitcoin.it/wiki/Timelock)  
以unix timestamp表示，類似於`1511321691`如果填入該欄位的值小於五億，則會把該數字視為區塊高度，意思為在該區塊高度之前不能將交易加入區塊

![](/assets/09isd.png)

> [https://en.bitcoin.it/wiki/Protocol\_documentation\#tx](https://en.bitcoin.it/wiki/Protocol_documentation#tx)

註2: 交易手續費，約為1000satoshis每KB.每個交易通常至少含有500 bytes.  
[https://bitcoinfees.earn.com/](https://bitcoinfees.earn.com/)

> [https://bitcoinfees.earn.com/](https://bitcoinfees.earn.com/)  
>  \(此網站可看到目前推薦的手續費與尚未確認的交易所含的手續費\)  
> 注意:他是以bytes為單位

註3: build transaction在coinb.in的原始碼  
[https://github.com/OutCast3k/coinbin/blob/217897285e51cbc33bdba3ec275aa3386ebf70b2/js/coin.js\#L793](https://github.com/OutCast3k/coinbin/blob/217897285e51cbc33bdba3ec275aa3386ebf70b2/js/coin.js#L793)

註4: bitcoinjs之transaction相關原始碼  
[https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction.js](https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction.js)  
[https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction\_builder.js](https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/transaction_builder.js)

