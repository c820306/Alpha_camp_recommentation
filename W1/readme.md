# Ruled-based recommendation

* 目的： 以訓練集資料使用Rule-Based推薦商品，並將推薦結果與測試集當中的客戶有申購商品進行比對，計算推薦成功率

* 什麼是Rule-Based?

  1. 以人為方式所制定的規則，通常無需大量資料即可制定，方法相對簡單、容易遵循，但缺點為相對僵硬。
  2. 以本次資料集主要是使用者申購產品後，針對每項產品留下評級的資料，若使用Rule-Based找到推薦模式，可從幾個維度切入：
     > * 產品留評級數量 高->低 
     > * 產品平均評級 高->低
     > * 近三個月留言 （較有機會反應近期爆品資訊）
        
說明過程如下:
1. 使用資料 ： http://deepyeti.ucsd.edu/jianmo/amazon/categoryFilesSmall/All_Beauty.csv
    * All_Beauty.csv欄位： asin(商品ID)、reviewerID(評論者ID)、overall(評論者留下的評級)、unixReviewTime(評論者留評級時間)
2. 資料整理
    * unixReviewTime(評論者留評級時間)--> 更改為年月日格式
3. 資料切分
    * 訓練資料拆分兩種情境：以'2018-09-01'前分析、以'2018-06-01'～'2018-09-01'分析近三個月
    * 測試資料：'2018-09-01'～'2018-09-30'
4. 產生推薦
     使用All_Beauty欄位：asin(商品ID)、reviewerID(評論者ID)、overall(評論者留下的評級)，以asin為出發計算商品評論數、平均評級，將時間拆分兩個區間，結合各維度檢視，說明如下：
    | method                           |排序欄位                          | 升降冪                 |    資料區間               |
    |----------------------------------|:-------------------------------:|----------------------:|-------------------------:|
    | reviewer_count                   | ['reviewer_count']              | ascending=(False)     |'2018-09-01'前             |
    | avg_rating                       | ['avg_rating']                  |ascending=(False)      |'2018-09-01'前             | 
    | reviewer_count> avg_rating       | ['reviewer_count','avg_rating'] |ascending=(False,False)|'2018-09-01'前             |
    | avg_rating> reviewer_count       | ['avg_rating','reviewer_count'] |ascending=(False,False)|'2018-09-01'前             |
    | nearly 3_months: reviewer_count  | ['reviewer_count']              |ascending=(False)      |'2018-06-01'～'2018-09-01' |
    | nearly 3_months: avg_rating      | ['avg_rating']                  |ascending=(False)      |''2018-06-01'～'2018-09-01'|

5. 推薦結果
    | method                         | 推薦準確度 | 建議選擇方案|
    |--------------------------------|:--------:|:---------:|
    | reviewer_count                 |  8.31 %  |-          |
    | avg_rating                     |   0.0 %  |-          |
    | reviewer_count> avg_rating     |  8.31 %. |-          |
    | avg_rating> reviewer_count     |  0.0 %   |-          |
    | nearly 3_months: reviewer_count|  15.42 % |Best       |
    | nearly 3_months: avg_rating    |  0.17 %  |-          |
6. 結論
     > 使用近三個月的reviewer_count所選出的商品推薦表現最佳，準確度達15.42%，主要原因可能為留言數無法直接表示商品購買量，但能表示商品購買熱度，且近三個月更能表示近期大家關注的商品。
    
