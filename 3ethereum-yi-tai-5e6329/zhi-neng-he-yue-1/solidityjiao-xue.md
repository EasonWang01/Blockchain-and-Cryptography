## Solidity

本章節將介紹撰寫智能合約的語言：Solidity

http://solidity.readthedocs.io/en/develop/solidity-in-depth.html

我們可以使用Solidity網頁版IDE：https://remix.ethereum.org，進行測試與部屬。



#### 1.版本

```
pragma solidity ^0.4.0;
```

一開始在程式最上面會指定使用之版本。

#### 2.合約名稱

```
contract TestContract {
 .....
}
```

每個合約會用合約名稱加上大括號 { } 包住。

#### 3.引入其他合約

```
import "filename";
import "filename" as symbolName;
import * as symbolName from "filename";
import {symbol1 as alias, symbol2} from "filename";
```

以上四種方式都可以。

# 資料型別





