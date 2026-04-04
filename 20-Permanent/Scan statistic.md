---
domain: DS
source: Original
tags: [change-point-detection, hypothesis-testing]
---
> [!abstract]
> 在某一個連續變數（如時間軸）上，檢測是否存在「異常聚集」（Clusters）

## 核心概念

沿著連續變數滑動觀察窗口，嘗試不同的窗口大小，每次掃描所有資料後計算最大似然比（max LR）。

## 假設檢定

> **H₀**：沒有異常群聚，窗內窗外一樣，共用一個 global Poisson($\lambda_0$) 分布
>
> **H₁**：有異常群聚，窗內窗外不同，各自一個 [[Poisson 分布是連續單位下事件發生次數的機率分布|Poisson]]($\lambda_1, \lambda_2$) 分布

## 實例

- **全局情況**：100 cases / 1000 exposure
- **窗內**：10 cases / 200 exposure
- **窗外**：90 cases / 800 exposure

## 似然比計算

### L(H₀)：共用一個 global 分布

==用 global ratio 計算：窗內資料出現機率 × 窗外資料出現機率==

1. 窗內期望次數：$200 \times \frac{100}{1000} = 20$
2. 窗外期望次數：$800 \times \frac{100}{1000} = 80$
3. 窗內資料出現機率：$P(\text{win}_內) = P(K_{in} = 10 \mid \mu_{in} = 20)$
   - Poisson 分布在平均 = 20 下，出現 K = 10 的機率
4. 窗外資料出現機率：$P(\text{win}_外) = P(K_{out} = 90 \mid \mu_{out} = 80)$
   - Poisson 分布在平均 = 80 下，出現 K = 90 的機率
5. $L(\text{H}_0) = P(\text{win}_內) \times P(\text{win}_外)$

### L(H₁)：窗內窗外各自一個 Poisson 分布

==窗內資料出現機率 × 窗外資料出現機率==

1. 窗內 Poisson 參數：$\lambda_1 = 10$（期望次數：ratio × exposure）
2. 窗外 Poisson 參數：$\lambda_2 = 90$（期望次數：ratio × exposure）
3. 窗內資料出現機率：$P(\text{win}_內) = P(K_{in} = 10 \mid \mu_{in} = 10)$
   - Poisson 分布在平均 = 10 下，出現 K = 10 的機率
4. 窗外資料出現機率：$P(\text{win}_外) = P(K_{out} = 90 \mid \mu_{out} = 90)$
   - Poisson 分布在平均 = 90 下，出現 K = 90 的機率
5. $L(\text{H}_1) = P(\text{win}_內) \times P(\text{win}_外)$

## 似然比統計量

$$LR = \frac{L(H_1)}{L(H_0)}$$

每次滑動都計算 LR，找出 max LR（群聚性最強的時段）。

## 統計顯著性檢驗

1. 隨機排序 case 位置，計算所有窗口的 max LR
2. 重複多次（如 999 次）
3. 加上真實資料的 max LR，共有 1000 個值
4. 排序後，若真實 max LR 在前 5% 代表 p-value < 0.05
   - 說明這個群聚是真實存在的，不是巧合

## 相關概念
- [[Poisson 分布是連續單位下事件發生次數的機率分布]] — Scan statistic 的基礎分布假設
- [[LR Test]] — 似然比檢定的通用方法
- [[Activity Monitor Operator Characteristic（AMOC）]] — 變化點偵測的效能評估工具