# 智能合約

智能合約可以被部屬到區塊鏈上並且被執行，每個合約都會有一個地址，而合約的邏輯是用程式寫成，目前主要開發語言為Solidity，智能合約類似於飲料販賣機，當他被部屬後想要執行交易或是執行合約上的function時，只要花費相對應的以太幣或是Gas，並且指定合約的地址以及你想要執行合約的function即可。執行後的結果會如同一般交易一樣被寫入區塊鏈中。

本章節將帶領讀者實際參與智能合約的開發過程，並且部屬合約以及執行操作。

# 實作

1.

我們先啟動Geth的開發用測試鏈

```
geth --dev --rpc --rpcport 8545 --datadir ./Ethtest --rpccorsdomain="*"  console
```

> --dev 是方便開發者開發的指令，會自動創建一個帳號裡面包含一定數量的Ether
>
> --rpccorsdomain="\*" 記得要加上，這樣才能讓外面連線到Geth

2.

開啟Remix 網頁IDE : [http://remix.ethereum.org](http://remix.ethereum.org)

> 這邊記得使用http的網址，因為如果是使用https網址將無法連到本地端http protocol的Geth

然後在Remix網頁右上角選擇到Run的Tab，之後下拉選單選擇Web3 Provider，再來輸入框記得填寫與本地Geth啟動相同之的HTTP RPC PORT

![](/assets/34534543.png)

3.

之後Remix連線到本地節點後即可看到Remix顯示本地節點的帳號資料。

![](/assets/92831.png)之後我們在左側程式部分改為如下

```js
pragma solidity ^0.4.19;

contract SimpleStorage {
  string storedData;

  // 建構子，部屬合約後即會執行
  function SimpleStorage(string x) public {
    storedData = x;
  }

  function set(string x) public {
    storedData = x;
  }

  function get() public constant returns (string) {
    return storedData;
  }
}
```

> 此為一個簡單設定字串和讀取字串的合約

之後點選右上方的Run，然後下方輸入建構子參數 \( 也就是初始化合約的參數\)，然後點選Create![](/assets/92011.png)

4.

部屬合約後我們可以看到區塊鏈相關訊息:

![](/assets/81122.png)

> 輸入web3.eth.getTransactionReceipt\(\) 查看相關交易訊息

![](/assets/0192123.png)

之後部屬合約後即可以點擊網頁右下方的function執行

![](/assets/832111.png)

> 記得字串要加上雙引號



