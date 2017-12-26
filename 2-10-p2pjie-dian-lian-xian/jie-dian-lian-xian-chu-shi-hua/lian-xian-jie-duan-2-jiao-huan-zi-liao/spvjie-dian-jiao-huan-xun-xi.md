比特幣之區塊鏈大小非常龐大，如果下載所有資料後需要佔據大約 100GB 左右的硬碟儲存空間，所以後來發展出了一種方法，可以不用下載整個區塊鏈的節點，稱為 SPV節點 \( Simplified Payments Verification nodes \)。



上一章節的節點資料交換為Full Node \(全節點\) 的資料交換方式，而這章節將講解可以有效節省空間的SPV node，SPV node只會下載 block header 而不像 Full node 會下載每個區塊裡面的所有 transaction。

