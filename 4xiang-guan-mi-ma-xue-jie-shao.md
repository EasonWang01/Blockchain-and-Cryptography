密碼學被廣泛應用在現今的應用中，例如晶片、信用卡、電子郵件加密傳輸、身分認證、遠端電腦連線等等。

其中包含許多協定例如:HTTPS、PGP、IPsec、SSL / TLS、SSH。

本章節將介紹雜湊函式\(Hash\)、對稱式加密、非對稱式加密，並且會以Node.js與OpenSSL結合範例與概念。

---

## OpenSSL

可使用以下指令來查看OpenSSL版本

```
openssl version -a
```

OpenSSL為一個開放原始碼的函式庫套件，用C語言寫成，類Unix系統已經內建在裡面，而Windows使用者可以用Git Bash來執行。

> OpenSSL計劃在1998年開始，其目標是發明一套自由的加密工具，其包含目前大部分的主流加密方法，但其原始碼之程式編寫有些雜亂。

查看所有可用指令與可用加密法

```
openssl help
```

![](/assets/9444.png)

而我們也可以輸入以下指令，測試電腦加密之性能與耗時

```
openssl speed
```

---

## Node.js Crypto模組

可使用以下指令來查看Node.js crypto模組

```
node -p crypto
```

取得可用Hash function

```
node -p crypto.getHashes()
```

取得可用之對稱式加密方法

```
node -p crypto.getCiphers()
```



