# 2-1 Bitcoin 介紹
> The block chain provides Bitcoin’s public ledger, an ordered and timestamped record of transactions. This system is used to protect against double spending and modification of previous transaction records.

> Each full node in the Bitcoin network independently stores a block chain containing only blocks validated by that node. When several nodes all have the same blocks in their block chain, they are considered to be in consensus. The validation rules these nodes follow to maintain consensus are called consensus rules. This section describes many of the consensus rules used by Bitcoin Core.

https://bitcoin.org/en/developer-guide#block-chain-overview

比特幣由位於世界各地的許多台電腦共同組成`比特幣網路`，每台電腦在完全同步比特幣鏈上的資料後即成為`全節點(Full Node)`，每個全節點都擁有區塊鏈上所有資料，並且可以進行挖礦的動作

比特幣的礦工們利用挖礦賺取比特幣，挖礦的過程等同於協助驗證交易與區塊

最後組成一個公開帳本`Bitcoin’s public ledger`

人們用比特幣來進行交易與投資