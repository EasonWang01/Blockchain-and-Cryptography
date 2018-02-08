# Ethereum 錢包

錢包種類包含有網頁版、桌面版、手機APP，還有適合開發者使用的 Command Line 介面錢包，紙本錢包、USB硬體錢包以及各個交易所保管的錢包等等，但使用上之安全性需要讀者自行評估。

本章節列出較常見的以太坊錢包：

# Mist

下載:

[https://github.com/ethereum/mist/releases](https://github.com/ethereum/mist/releases)

Mist 是一個用 [electron](https://github.com/atom/electron) 框架寫成之桌面應用程式，可用其部署與執行合約。

![](/assets/螢幕快照 2018-01-13 下午11.36.17.png)

> 執行合約前會需要先加入合約，但目前介面上沒有移除按鈕，可以使用以下方法移除加入的所有合約：
>
> ```js
> 開啟console: alt+ctrl+i 或是 option+command+i
>
> 輸入:CustomContracts.find().fetch().forEach(d => CustomContracts.remove(d))
> ```

## MetaMask

為一個 Chrome 瀏覽器的 Plugin，可以用來產生地址、接送與發送交易，為目前廣為使用的 Ethereum 瀏覽器 Plugin，因為可用其直接簽發 Dapp 的相關交易。

[https://metamask.io/](https://metamask.io/)

![](/assets/09.png)

## wallet.ethereum

[https://wallet.ethereum.org/](https://wallet.ethereum.org/)

其會讀取 MetaMask 上的資料，並顯示在此網頁上，可用來發送交易與部屬合約。

> 與Mist介面相同

![](/assets/9091.png)

## MyEtherWallet

[https://www.myetherwallet.com/](https://www.gitbook.com/book/easonwang01/e/edit#)

打開瀏覽器網頁就能直接使用，也可以離線產生交易訊息，以及可以引入`.keystore`檔案。

並且可以利用其註冊ENS : [https://www.myetherwallet.com/\#ens](https://www.myetherwallet.com/#ens)

> ENS類似於以太坊的域名系統，可以把地址用域名表示。
>
> [https://ens.domains/](https://ens.domains/)

![](/assets/螢幕快照 2018-02-02 上午10.16.25.png)

# EtherWall

為一個桌面應用程式版本的錢包。

[https://www.etherwall.com/](https://www.etherwall.com/)

![](/assets/螢幕快照 2018-01-14 下午2.07.59.png)

# Geth

將會在之後章節詳細介紹，為命令列介面，適合開發者使用，可以執行節點，並且提供JSON-RPC，也可以當作錢包使用。

[https://geth.ethereum.org/downloads/](https://geth.ethereum.org/downloads/)

# Parity

目標是成為一個輕量快速的Ethereum implementation

> Fast, light, robust Ethereum implementation

[https://github.com/paritytech/parity/releases](https://www.gitbook.com/book/easonwang01/blockchain/edit#)

安裝後啟動，會自動開啟一個網頁可以方便使用者操作。

![](/assets/bd4dbf77-75f5-4fcd-8ea4-e8f88e0d8be4.png)

