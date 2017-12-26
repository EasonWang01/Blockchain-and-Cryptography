比特幣之區塊鏈大小非常龐大，如果下載所有資料後需要佔據大約 100GB 左右的硬碟儲存空間，所以後來發展出了一種方法，可以不用下載整個區塊鏈的節點，稱為 SPV節點 \( Simplified Payments Verification nodes \)。

上一章節的節點資料交換為Full Node \(全節點\) 的資料交換方式，而這章節將講解可以有效節省空間的SPV node，SPV node只會下載 block header 而不像 Full node 會下載每個區塊裡面的所有 transaction。

SPV節點使用Merkle Root驗證特定交易是否存在於區塊中，不用管其他不相關的交易，而在安全性考量下，SPV節點通常會有許多個連線的Full Node來確保傳過來的資料是正確的。



所以在SPV節點中不用像上一章節發出 getdata 來請求資料，而是發出 getheader。

