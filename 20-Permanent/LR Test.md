---
domain: DS
source: Original
tags: [hypothesis-testing]
---

> [!abstract] 透過比較兩個模型的似然值，判斷較複雜的模型是否顯著優於較簡單的模型

## 核心概念

**似然值 L(Model)**：給定這組數據，這個模型（參數）生成它的機率有多大？

LR Test 比較兩個**巢套模型**（nested models）：
- **H₀（受限模型）**：較簡單，參數較少
- **H₁（完整模型）**：較複雜，包含 H₀ 的所有參數加上額外參數

## 檢定統計量

$$LR = -2 \ln \frac{L(\hat{\theta}_0)}{L(\hat{\theta}_1)} = -2 [\ell(\hat{\theta}_0) - \ell(\hat{\theta}_1)]$$

- $L(\hat{\theta}_0)$：受限模型的最大似然值
- $L(\hat{\theta}_1)$：完整模型的最大似然值
- $\ell$：log-likelihood

> 完整模型的似然值一定 ≥ 受限模型，所以 LR ≥ 0

## 判定方式

在 H₀ 為真的情況下，LR 統計量漸近服從卡方分布：

$$LR \xrightarrow{d} \chi^2(k)$$

其中 k = 兩個模型的參數數量差。若 LR > 臨界值，拒絕 H₀，代表額外參數有顯著貢獻。

## 三大假設檢定方法的比較

| 方法 | 需要估計的模型 | 核心思路 |
|:---:|:---:|:---|
| LR Test | H₀ 和 H₁ 都要估計 | 比較兩個模型的似然值差距 |
| Wald Test | 只估計 H₁ | 參數估計值離 H₀ 的限制有多遠 |
| [[Langrange Multiplier Test]] | 只估計 H₀ | 在 H₀ 限制下，加入新參數的邊際改善有多大 |

## 相關概念
- [[Chi-square test]] — LR 統計量漸近服從卡方分布
- [[Langrange Multiplier Test]] — 三大檢定方法之一，只需估計受限模型
- [[Supremum Lagrange Multiplier Test]] — LM Test 的結構斷裂擴展
