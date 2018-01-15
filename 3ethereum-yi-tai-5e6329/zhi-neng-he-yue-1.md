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



