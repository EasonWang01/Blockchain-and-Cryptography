# 環境安裝

# 1.Node.js與NPM安裝

在本書範例中許多範例使用Node.js來執行，可至Node.js官方網站選擇自己的作業系統版本然後下載並安裝  
[https://nodejs.org/en/download/](https://nodejs.org/en/download/)

安裝Node.js時會自動安裝npm，npm為Node.js的套件管理工具

下載套件的方式：

```
npm install <套件名稱>
npm install <套件名稱> -g
```

\(上面的`-g` 全域安裝方式，可以讓套件安裝後在Terminal中的任何路徑直接輸入套件名稱執行，但如果是想在`js`檔案內直接使用`require`引入全域套件的話，要再加上`NODE_PATH`環境變數，如此即可直接引用全域安裝的套件，不用在每個專案資料夾個別安裝套件。

#### 加入環境變數:

設定以下環境變數：

`NODE_PATH` ， 安裝全域包後給 `js`檔案`require` 使用  
Windows：

```
NODE_PATH C:\Program Files\nodejs\node_modules
```

macOS：

```
export NODE_PATH=/usr/local/lib/node_modules
```

如果不知道路徑是什麼可以先試著安裝 `-g` 模組，然後看一下他印出來的安裝路徑。

![](/assets/環境變數.png)

#### 切換Node.js版本

有兩個常用的第三方模組，可以方便快速切換Node.js的版本。

#### 1.`n`

```
n use <version>
```

#### 2.`nvm`

> （windows可能無法使用`n`，所以可以用`nvm`安裝檔）  
> 下載點:  
> [https://github.com/coreybutler/nvm-windows/releases](https://github.com/coreybutler/nvm-windows/releases)

使用方式:

```
nvm install <version>
nvm use <version>
```

#### 更新或復原npm版本

（把數字改為你要的版本即可）

```
npm install npm@4.6.1
```

> 另外建議可以使用 `yarn` 來安裝套件，安裝方式： `npm install yarn -g`
>
> 之後使用：`yarn add <package>` 來安裝第三方套件

#### 查看相關套件資訊與其歷史版本

```
npm view <套件名稱>
```

# 2.cURL

功能為用來簡單發出request，除了macOS與Linix以外，Windows沒有此指令，Windows可以到以下網站下載，之後解壓縮後加入到環境變數中。

[https://curl.haxx.se/download.html](https://curl.haxx.se/download.html)

> 或是使用Git bash，即可使用cURL指令。

# 3.Git

為目前常見的版本控制工具，在本書中許多從 Github clone專案時會使用到。

[https://git-scm.com/](https://git-scm.com/)

# 4.Docker

可以快速地啟動一個輕量的虛擬化環境，方便大家可以在相同環境開發、測試與部屬。

[https://www.docker.com/](https://www.docker.com/)

