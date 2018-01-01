# 有關Hash

Hash function也可稱為**雜湊函式，**主要功能是把一串不定長度大小的字串或數字，轉換一串為固定大小的值。

通常是一個單向函式，也就是無法從算出來的值反推回原本的值 \(除了已經被破解的Hash算法例外\)。

比較常見的包含MD5、SHA1、SHA2、SHA3與ripemd系列等等，但目前MD5與SHA1已經被破解。

而還有一些是專門用來幫密碼做Hash的雜湊函式，例如：

[bcrypt](https://www.gitbook.com/book/easonwang01/e/edit#)、[PBKDF2](https://www.gitbook.com/book/easonwang01/e/edit#)、[scrypt](https://www.gitbook.com/book/easonwang01/e/edit#)、[Argon2](https://www.gitbook.com/book/easonwang01/e/edit#)等等

