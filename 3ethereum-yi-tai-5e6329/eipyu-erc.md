# EIP \( The Ethereum Improvement Proposal \)

[https://github.com/ethereum/EIPs](https://github.com/ethereum/EIPs)

為以太坊的改進提案，類似於Bitcoin的BIP，社群所有人均可發起EIP提案，其下分為四個種類

```
1.Core
2.Networking
3.Interface
4.ERC
```

其提案流程如下圖

![](/assets/process.png)

> [https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1.md](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1.md)

# ERC

屬於EIP提案的一種類型，目前已經正式核可的ERC提案如下

#### 1.ERC-20 \(Token Standard\)

[https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md)

描述在智能合約上的token該有的interface。

#### 2.ERC-55 \(Mixed-case checksum address encoding\)

[https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md)

由地址產生出checksum，官方範例如下：

```js
const createKeccakHash = require('keccak')

function toChecksumAddress (address) {
  address = address.toLowerCase().replace('0x', '')
  var hash = createKeccakHash('keccak256').update(address).digest('hex')
  var ret = '0x'

  for (var i = 0; i < address.length; i++) {
    if (parseInt(hash[i], 16) >= 8) {
      ret += address[i].toUpperCase()
    } else {
      ret += address[i]
    }
  }

  return ret
}

> toChecksumAddress('0xfb6916095ca1df60bb79ce92ce3ea74c37c5d359')
'0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359'
```

#### 3.ERC-162 與 ERC-181 \(ENS support for reverse resolution of Ethereum addresses\)

有關以太坊上註冊域名Ethereum Name Service，類似於DNS。

#### 4.ERC-190 \(Ethereum Smart Contract Packaging Standard\)

有關智能合約的打包與編譯時的安全性與程式碼複用。

目前列出符合此標準的將包含以下Ethereum 相關框架

```
Truffle
http://truffleframework.com/

Populus
http://populus.readthedocs.io/en/latest/

Dapple
http://dapple.readthedocs.io/en/master/

Eris PM
https://github.com/monax/monax

Embark
https://github.com/iurimatias/embark-framework

Browser Solidity
https://github.com/ethereum/browser-solidity
```



