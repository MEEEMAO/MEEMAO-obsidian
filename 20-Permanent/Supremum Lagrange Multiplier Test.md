---
domain: DS
source: Original
tags: ['hypothesis-testing', 'time-series']
---

> [!abstract]
> 檢測時間序列或迴歸模型中是否存在結構性轉變（Structural Break）：模型在什麼時候「變了樣」？

## 核心概念

Supremum LM Test 是在所有可能的轉變位置上進行 [[Langrange Multiplier Test|LM 檢定]]，取出最大的 LM 統計量來判斷是否存在顯著的結構轉變。

## 執行步驟

1. **遍歷所有可能的轉變位置**
   - 時間點、資料空間（如 x1 = TRUE/FALSE）

2. **計算每個位置的 LM test**
   - 假設存在一個新的變化因子：「這個時間點 t 之前/之後」
   - 評估這個新因子對模型參數的影響有多大

3. **取出最大值（Supremum）**
   - 從所有 LM 統計量中選出最大值
   - 若 Sup LM 超過臨界值，代表該數據集中極可能存在一個結構轉變點

## 應用場景

| 領域 | 應用例 |
|:---|:---|
| 宏觀經濟分析 | 檢測政策改變（如升息、稅制改革）是否導致經濟走勢永久性偏移 |
| 金融市場 | 判斷股市是否存在泡沫破裂點，或市場波動性是否突然放大 |
| 品質控管 | 監控生產線數據，確認機器設備是否在某個時間點開始異常偏移 |

## 相關概念
- [[Langrange Multiplier Test]] — Supremum LM Test 的基礎檢定方法
- [[LR Test]] — 另一種假設檢定方法
- [[Scan statistic]] — 類似的異常聚集偵測方法
