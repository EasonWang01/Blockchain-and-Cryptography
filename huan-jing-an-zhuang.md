#Node.js與npm安裝

在本書範例中許多範例使用Node.js來執行，可至Node.js官方網站選擇自己的作業系統版本然後下載並安裝
https://nodejs.org/en/download/

安裝完Node.js後會自動安裝npm，npm為Node.js的套件管理工具

下載套件的方式
```
npm install
npm install -g 
```

(上面的`-g`使用後可在cmd的任何路徑輸入package名稱執行，但如果是想在js檔內直接使用require的話，要再把環境變數加上才行，如此即可不用在每個專案資料夾個別安裝package，記得環境變數名稱要是NODE_PATH)

####共有兩個環境變數:

一個是`NODE_PATH` => 安裝全域包後給js檔案require用
Windows
```
NODE_PATH    C:\Program Files\nodejs\node_modules
```
Mac 
```
export NODE_PATH=/usr/local/lib/node_modules
```
如果不知道路徑是什麼可以先試著安裝-g 模組 然後看一下他印出來的安裝路徑

![](/assets/環境變數.png)


#切換Node.js版本
n
n use <version>
nvm
(windows可能無法使用n,所以可以用nvm安裝檔)
下載點:
https://github.com/coreybutler/nvm-windows/releases

然後
nvm install <version>
nvm use <version>


#更新或復原npm版本
(把數字改為你要的版本即可)
npm install npm@4.6.1


