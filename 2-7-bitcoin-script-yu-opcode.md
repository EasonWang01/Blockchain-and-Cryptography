# 2-7 Bitcoin Script 與 OPCODE

比特幣使用Script腳本來執行相關的邏輯，以下是一個交易的腳本範例

\(結合了unlocking script與locking script的完整腳本\)

```
<A Signature> <A Public Key> OP_DUP OP_HASH160 <A Key Hash> OP_EQUAL OP_CHECKSIG
```

然後他執行方式我們可以想像有一個堆棧，其方式為先進後出

我們先從最左邊開始看:

#### 第一步:

&lt;A Signature&gt; 為經過私鑰的簽名字串先推入堆棧內，在來往右邊一步，把&lt;A Public Key&gt;也推入

![](/assets/89.png)

#### 第二步:

再來是OP\_DUP 與 OP\_HASH160

OP\_DUP 會把前一個內容複製，所以目前最上面的Public Key會被複製

```
Public Key
Public Key
A Signature
```

#### 第三步:

然後下一個進入的是OP\_HASH160，他會把他前一個值做RIPEMD160\(SHA256\(上一個值\)\)之計算，所以在這裡他會把上個推進去的Public Key計算為Public Key Hash

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

這個會使用非對稱加密演算法，來確認這個Public Key能不能配對上這個用私鑰簽發出的Signature，如果成功就會返回true

所以最後堆棧內只剩下True，代表這筆交易的Script成功執行，所以就可以正常地發出交易了。

> 有關其他OPCODE的功能可以參考以下著個連結:
>
> [https://en.bitcoin.it/wiki/Script](https://en.bitcoin.it/wiki/Script)

### Script對應之Hex碼

交易中有output script \(scriptPubKey\)與input script \(scriptSig\)

這邊我們在複習一次交易的內容如下:

![](/assets/45.png)

其中包含上一筆交易後被鎖住UTXO之output區塊的scriptPubKey![](/assets/24.png)

以及包含要花費這筆錢時要解鎖的unlock script也就是input區塊的ScriptSig![](/assets/23.png)



