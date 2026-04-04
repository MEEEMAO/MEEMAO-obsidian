---
domain: DS
source: Original
tags: ['hypothesis-testing']
---
> [!abstract]
> 「如果我們改變模型（加入或移除某個因子 $x_{new}$ ），模型會產生多大的波動？」

## 核心概念

限制模型不使用 $x_{new}$ 進行訓練，事後判斷 $x_{new}$ 會對現有模型貢獻多少。Score 總和的改變量（斜率大小、參數調整幅度）反映了 $x_{new}$ 與殘差的相關性。

## 計算方式

假設有三個因子 (x1, x2, x3)，想要判斷 x2 對模型的影響：

1. 用 x1 & x3 預測 y，訓練模型
2. 得到每筆資料的殘差
3. 計算 x2 的 score：

$$Score = \sum (y_i - \hat{y}_i) \cdot x_{i,2}$$

> [!important]
> 如果新因子 $x_{new}$ 與誤差 $(y - \hat{y})$ 的方向一致（相關性高），相乘總和（內積）就會很大。這代表：「這個新因子剛好可以解釋那些目前模型解釋不了的部分。」

## 檢定三兄弟的比較

| 方法 | 核心思路 |
|:---|:---|
| [[Wald Test]] | 直接看 $x_{new}$ 的相關係數 |
| [[LR Test]] | 訓練 2 個模型（有加 $x_{new}$ vs 沒加 $x_{new}$），直接比較 loss |
| [[Langrange Multiplier Test]] | 在 H₀ 限制下，評估加入新參數的邊際貢獻 |

## 相關概念
- [[Wald Test]] — 三大檢定方法之一，直接估計完整模型
- [[LR Test]] — 三大檢定方法之一，比較兩個模型的似然值
- [[Supremum Lagrange Multiplier Test]] — LM Test 在結構斷裂檢測上的應用