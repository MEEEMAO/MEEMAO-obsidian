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

> **H₀**：沒有異常群聚，窗內窗外一樣，共用一個 global Binomial ($p_0,n_0$) 分布
>
> **H₁**：有異常群聚，窗內窗外不同，各自一個Binomial ($p_1,n_1$)($p_2,n_2$)  分布

## 實例

- **全局情況**：100 cases / 1000 exposure (p=0.1)
- **窗內**：30 cases / 100 exposure (p=0.3)
- **窗外**：70 cases / 900 exposure (p=0.07)

## 似然比計算
![[telegram-cloud-photo-size-5-6241997356102520864-w.jpg|617]]
### L(H₀)：共用一個 global 分布

==用 global ratio 計算：窗內資料出現機率 × 窗外資料出現機率==

1. 窗內期望次數：$100 \times \frac{100}{1000} = 10$
2. 窗外期望次數：$900 \times \frac{100}{1000} = 90$
3. 真實窗內資料出現機率 $P(\text{win}_內)$：
   - Binomial 分布下 (p=0.1, n=100)，出現 30 的機率
4. 真實窗外資料出現機率$P(\text{win}_外)$：
   - Binomial 分布下 (p=0.1, n=900)，出現 70 的機率
 
 $L(\text{H}_0) = P(\text{win}_內) \times P(\text{win}_外)$

### L(H₁)：窗內窗外各自一個 Binomial 分布

==窗內資料出現機率 × 窗外資料出現機率==

1. 真實窗內資料出現機率 $P(\text{win}_內)$：
   - Binomial 分布下 (p=0.3, n=100)，出現 30 的機率
2. 真實窗外資料出現機率$P(\text{win}_外)$：
   - Binomial 分布下 (p=0.07, n=900)，出現 70 的機率

$L(\text{H}_1) = P(\text{win}_內) \times P(\text{win}_外)$

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
- [[LR Test]] — 似然比檢定的通用方法
