# 以太坊錢包

本章節列出較常見的以太坊錢包，但使用上之安全性需要讀者自行評估。

# Mist

下載:

[https://github.com/ethereum/mist/releases](https://github.com/ethereum/mist/releases)

Mist是一個用[electron](https://github.com/atom/electron)框架寫成之桌面應用程式。

![](/assets/螢幕快照 2018-01-13 下午11.36.17.png)

其可以部署以及加入合約。

> 移除加入的所有合約
>
> ```js
> 開啟console: alt+ctrl+i 或是 option+command+i
>
> 輸入:CustomContracts.find().fetch().forEach(d => CustomContracts.remove(d))
> ```

## MetaMask

為一個Chrome瀏覽器的Plugin，可以用來產生地址、接送與發送交易。

[https://metamask.io/](https://metamask.io/)

![](/assets/09.png)

## wallet.ethereum

[https://wallet.ethereum.org/](https://wallet.ethereum.org/)

其會讀取MetaMask上的資料，並顯示在網頁上，可用來發送交易與部屬合約。

> 與Mist介面相同

![](/assets/9091.png)

## MyEtherWallet

打開瀏覽器網頁就能直接使用，為較多人選擇之錢包。

[https://www.myetherwallet.com/](https://www.myetherwallet.com/)

![](/assets/螢幕快照 2018-01-13 下午11.00.08.png)

# EtherWall

為一個桌面應用程式版本的錢包。

[https://www.etherwall.com/](https://www.etherwall.com/)

![](/assets/螢幕快照 2018-01-14 下午2.07.59.png)

# Geth 

將會在之後章節詳細介紹，其主要為命令列介面，適合開發者使用，可以單獨跑節點，並且提供JSON-RPC可供呼叫，也可以當作錢包使用。

https://geth.ethereum.org/downloads/

# Parity

目標是成為一個輕量快速的Ethereum implementation

> Fast, light, robust Ethereum implementation

[https://github.com/paritytech/parity/releases](https://www.gitbook.com/book/easonwang01/blockchain/edit#)

安裝後啟動，會自動開啟一個網頁可以方便使用者操作。

![](/assets/bd4dbf77-75f5-4fcd-8ea4-e8f88e0d8be4.png)

