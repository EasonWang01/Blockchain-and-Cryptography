# Regtest Mode

有時開發者需要進行比特幣區塊鏈測試，這時Regtest Mode可以提供一個從零開始的區塊鏈環境，在此模式下，不需要和比特幣網路其他節點互動，省去了節點同步的過程。

### 使用Bitcoin-QT執行Regtest Mode

我們可以輸入以下指令，讓`Bitcoin-QT`在`Regtest Mode`下執行。

```
./bitcoin-qt -regtest
```

> 如果是Windows作業系統，請將前面`./`省略，改為: `bitcoin-qt.exe -regtest`

Bitcoin-qt 執行 Regtest Mode:![](/assets/螢幕快照 2017-12-26 下午3.53.17.png)

## 使用Bitcoin-CLI

我們也可以使用 Bitcoin-CLI 來與`-regtest`模式下的節點互動 :

1.先執行Bitcoin server

```
./bitcoind -regtest -daemon
```

2.產生500個區塊

```
./bitcoin-cli -regtest generate 500
```

![](/assets/螢幕快照 2017-12-26 下午4.16.37.png)3.查看餘額

```
./bitcoin-cli -regtest getbalance
```

4.查看可用指令

```
./bitcoin-cli -regtest help
```

5.取得節點資訊

```
./bitcoin-cli -regtest getblockchaininfo
```

![](/assets/螢幕快照 2017-12-26 下午4.25.28.png)

## 最後記得關閉Bitcoin server

```
./bitcoin-cli -regtest stop
```

### 重置Regtest chain

當我們想要重置 Regtest 區塊鏈時，將先前章節所提到的Bitcoin資料夾路徑下的 `regtest` 資料夾刪除即可。

e.g.

```
sudo rm -rf < Regtest 資料夾路徑 >
```



