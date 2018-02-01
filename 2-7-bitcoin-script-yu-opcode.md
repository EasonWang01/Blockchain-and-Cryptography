# 2-7 Bitcoin Script 與 OPCODE

比特幣使用 Script 腳本來執行相關的交易邏輯

> 以下為結合了 Unlocking script 與 Locking script 的完整腳本

```
<A Signature> <A Public Key> OP_DUP OP_HASH160 <A Key Hash> OP_EQUAL OP_CHECKSIG
```

其執行方式為堆棧：先進後出，類似於單一開口的瓶子，先放入的東西必須越後面取出。

#### 第一步:

Script 會從左邊開始執行 :

&lt;A Signature&gt; 為一個經過私鑰簽名的字串，其先推入堆棧內，在來往右邊一步，把 &lt;A Public Key&gt; 也推入。

![](/assets/89.png)

#### 第二步:

再來是OP\_DUP 與 OP\_HASH160

OP\_DUP 會把前一個內容複製，所以目前最上面的Public Key會被複製。

```
Public Key
Public Key
A Signature
```

#### 第三步:

然後下一個進入的是OP\_HASH160，他會把他前一個值做RIPEMD160\(SHA256\(上一個值\)\)之計算，所以在這裡他會把上個推進去的Public Key計算為Public Key Hash。

```
Public Key Hash
Public Key
A Signature
```

#### 第四步:

再來的兩個script分別為&lt;A Key Hash&gt; \(也就是Public Key Hash\)  與 OP\_EQUAL

我們先把&lt;A Key Hash&gt; 推入堆棧

```
Public Key Hash
Public Key Hash
Public Key
A Signature
```

然後OP\_EQUAL 會比較目前堆棧上的前兩個值是否相同，若相同返回 True 繼續，並且把剛才兩個比較的值消除

所以現在堆棧內長這樣

```
Public Key
A Signature
```

#### 第五步:

最後，剩下OP\_CHECKSIG

這個會使用非對稱加密演算法，來確認這個Public Key能不能配對上這個用私鑰簽發出的Signature，如果成功就會返回 True。

所以最後堆棧內只剩下 True，代表這筆交易的Script成功執行，所以就可以正常地發出交易了。

> 有關其他 OPCODE 的功能可以參考以下連結 :
>
> [https://en.bitcoin.it/wiki/Script](https://en.bitcoin.it/wiki/Script)

### Script對應之Hex碼

交易中有 Output script \(scriptPubKey\)與 Input script \(scriptSig\)

假設有一筆交易的內容如下:

```json
Transaction {
  version: 1,
  locktime: 0,
  ins:
   [ { hash: <Buffer d8 c8 df 6a 6f dd 2a dd af 58 9a 83 d8 60 f1 8b 44 87 2d 13 ee 6e c3 52 6b 2b 47 0d 42 a9 6d 4d>,
       index: 0,
       script: <Buffer 48 30 45 02 21 00 b3 15 57 e4 71 91 93 6c b1 4e 01 3f b4 21 b1 86 0b 5e 4f d5 d2 bc 5e c1 93 8f 4f fb 16 51 dc 89 02 20 26 61 c2 92 07 71 fd 29 dd 91 ... >,
       sequence: 4294967295,
       witness: [] },
  outs:
   [ { value: 155000000,
       script: <Buffer 76 a9 14 08 33 8e 1d 5e 26 db 3f ce 21 b0 11 79 5b 1c 3c 8a 5a 5d 07 88 ac> } ] 
}
```

看到Out的部分：為交易後被鎖住UTXO之scriptPubKey

| OP\_CODE | Number |
| :--- | :--- |
| OP\_DUP | 76 |
| OP\_HASH160 | a9 |
| PUSHDATA14 | 14 |
| Public Key Hash | 08 33 8e 1d 5e 26 db 3f ce 21 b0 11 79 5b 1c 3c 8a 5a 5d 07 |
| OP\_EQUALVERIFY | 88 |
| OP\_CHECKSIG | ac |



我們可以看到上圖許多OP開頭之字樣，而其後對應的Hex碼都是查表而來

![](/assets/34.png)

> 可在原始碼中src/script/script.h找到
>
> [https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/script/script.h](https://github.com/bitcoin/bitcoin/blob/3c098a8aa0780009c11b66b1a5d488a928629ebf/src/script/script.h)

或是可從以下網站直接查表:

[https://en.bitcoin.it/wiki/Script](https://en.bitcoin.it/wiki/Script)

![](/assets/kopsd9.png)

## 推入Bytes

在 Script 裡面如果含有要推入的byte資料，例如&lt;Public key&gt;，會需要先在其前面放上該Public key的byte長度。

例如：

```js
Input Scripts
ScriptSig: PUSHDATA(72)[3045022100fa279ddeea245c2074df379fd2d6259946c7cade3f0f04705d1ce0735ef3aefa02205f8ec36a9e46d1d064fe451a2a1ae9716585349257df47b5dbe5d5d59171a51701] PUSHDATA(33)[03c96078a78fdd7a6a935b17b3d888e067fe9226a19fd9aa75676aef06872d3898]

Output Scripts
DUP HASH160 PUSHDATA(20)[4ad738f7a2516011f4fcceec085224f4c27ed9bc] EQUALVERIFY CHECKSIG
```



