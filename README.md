# S&P500選擇權套利實作

### 抓取資料
https://hackmd.io/@s97302/HkwFoIZ5n

由WRDS取得2023/01/18~02/15的選擇權資料，存為csv檔案

並且從 investing.com 取得S&P500的指數和期貨資料

![](https://hackmd.io/_uploads/SyM7pIbc3.png)

在資料處理上，有增加每個選擇權的剩餘到期天數欄位(maturity_days)，理論上應該用有開盤的天數除上252天來計算T，但是因為計算效率，暫時只使用天數除以365來替代。

附上class的定義，方便程式理解

![](https://hackmd.io/_uploads/Bk8YT8bq2.png)

### 套利機會比對

之後將所有選擇權的履約價當作是索引，挑出所有相同履約價的選擇權，包含不同天的，依序驗證put call parity是否成立，驗證完後將其移除。由於S&P 500指數不能直接買賣，因此檢查時用index，但是真正要去執行套利時是使用futures去做。

　　假如是call的價錢比較高，就用它bid的價錢來賣出並加上需要借的錢K*e-r*T，減去買put需要的價格(以offer計算)和現貨價格。
	而真正計算利潤時，則將現貨指數改成期貨價格進行計算，因為指數無法直接買賣，並扣除交易手續費(1元)。

![](https://hackmd.io/_uploads/Bk34CUZch.png)

假如是put那邊的價格較高，就用它bid的價錢來賣出並加上放空現貨指數所得的錢，減去買call需要的價格(以offer計算)和需要存起來的現金，之後一樣在真正計算利潤時將現貨指數改成期貨價格進行計算，並且扣除交易手續費(1元)。

![](https://hackmd.io/_uploads/HkpSCIZc2.png)

### 執行結果

此為部分結果，總共數量約有九萬個套利機會
![](https://hackmd.io/_uploads/r1jLCUb52.png)

# 美債20年ETF套利

### 資料處理

資料包含元大和國泰的美債ETF每日價格，因為兩者的追蹤標的成分極為相似，所以可以在某方的淨值比大幅偏離時，進行套利交易

先對加入了淨值比表示一單位元大可以買多少的國泰，和當天一買一賣會產生的現金流，方便後面計算。

![](https://hackmd.io/_uploads/rJIrlPbc3.png)

資料會如同下面所示

![](https://hackmd.io/_uploads/rk9UxvWc3.png)

### 套利機會尋找

將手續費設定為0.1425%，融卷費為0.008%。如果在今天溢價過高出現時，執行一買一賣等淨值的ETF所出現的現金流，比在未來任何一天執行同樣的動作時產生的現金流還要多時，就會判定為有套利機會，當然還需要考慮交易成本等問題，因此在扣除成本後才會判定是否有利可套。

假設不知道未來情況如何，在一發現可以買回還卷獲利時就做，加上if判斷式中的break即可做到。

![](https://hackmd.io/_uploads/Sk-_lPZq3.png)

### 執行結果

![](https://hackmd.io/_uploads/S1D9lD-cn.png)



---
