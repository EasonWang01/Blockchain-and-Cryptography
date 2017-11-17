# 交易確認(confirmation)
> 當交易被區塊納入後，其確認數會增加一，之後又在此區塊後產生了一個區塊時此時確認數為二
Each additional confirmation is a new block being found and added to the end of the blockchain.

1.原生的比特幣客戶端程式在交易經過六個區塊確認之前都會顯示`n/unconfirmed`，為了避免雙重支付(double spending)

>Freshly-mined coins cannot be spent for 100 blocks

2.礦工挖礦獲得的比特幣收入必須在100個區塊確認後才可交易
> 寫在如下原始碼https://github.com/bitcoin/bitcoin/blob/1d9d314573ee48f6f51107265f1cf1fa9e36c998/src/consensus/consensus.h#L14

3.在以下網站可用來計算在特定的hashrate運算比例與確認數下可能被成功hack(double spend)的機率

https://people.xiph.org/~greg/attack_success.html
> 此網站之上面的輸入框`Proportion of hash-power`
1代表100%,0.1為10%


以下為寫在白皮書的公式
![](/assets/避免雙重支付公式.png)


有關其他可能被攻擊的方式
https://en.bitcoin.it/wiki/Irreversible_Transactions