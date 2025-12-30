# Data-mining - 🇦🇺 澳洲降雨預測系統
### Rain in Australia Prediction with Random Forest & Advanced Optimization

本專案為大學資料探勘課程之期末專題，旨在利用澳洲各地氣象觀測資料，建構能有效預測「隔日是否降雨」的分類模型。透過隨機森林 (Random Forest) 演算法、SMOTE 資料平衡技術以及決策閾值優化，實現了高準確度與高區辨力的預測效能。

[UI程式碼](UI.ipynb)
## 專案亮點
* **高區辨力模型：** 最終模型之 **AUC Score 達到 0.8900**，展現極強的分類能力。
* **精準優化策略：** 針對降雨預測的實務需求，採用 **F2-Score** 為優化目標，並將決策閾值調整至 **0.515** 以平衡誤報與漏報。
* **完整前處理流水線：** 包含 IQR 異常值截斷、地區分組補值、滯後特徵 (Lag Features) 與滾動平均 (Rolling Mean) 。
* **互動式介面：** 支援 **Gradio UI**，可實現即時氣象機率預測與視覺化展示 。

## 實驗結果
[cite_start]模型於獨立測試集（16,266 筆資料）上的評估結果如下 [cite: 480, 514]：

| 指標 (Metric) | 數值 (Value) |
| :--- | :--- |
| **總體準確率 (Accuracy)** | **85.43%** |
| **加權 F1-Score** | **0.8517** |
| **AUC Score** | **0.8900** |
| **Rain (1) 類別精確度 (Precision)** | **0.69** |
| **Rain (1) 類別召回率 (Recall)** | **0.63** |

### 混淆矩陣 (Confusion Matrix)
```text
[[11641 (TN)   1019 (FP)]
 [ 1351 (FN)   2255 (TP)]]
```
* TN (真陰性)：成功預測 11,641 筆晴天。
* TP (真陽性)：成功預測 2,255 筆降雨事件。
## 技術細節
1. 資料前處理 (Data Preprocessing)
* 缺失值填補：類別型欄位採眾數填補 ；Evaporation、Sunshine 等欄位採 Location 分組平均值填補 。
* 特徵工程：
  * 日期拆解：提取 Year, Month, Day, Weekday 特徵 。
  * 時序特徵：建立 1~2 日滯後特徵 與 3 日滾動平均值 。
* 異常值處理：使用 IQR Capping (factor=3) 處理 Rainfall 與 WindSpeed 等極端值 。
* 資料平衡：對訓練集應用 SMOTE 過採樣技術，解決 8:2 的類別不平衡問題 。
2. 模型建構 (Modeling)
* 演算法：隨機森林 (Random Forest) 。
* 最佳超參數：
  * n_estimators: 300 , max_depth: 30 , criterion: entropy
* 模型儲存：採 .pth 格式封裝，並儲存對應之 StandardScaler  以供預測介面使用。
3. 機率預測機制說明
* 「60% 下雨」的含義：代表森林中 300 棵決策樹中，有 180 棵樹根據觀測特徵投票判定為降雨。
* 決策門檻：經優化設定為 51.5%。當森林投票比例超過此門檻，系統即判定為預測下雨。
