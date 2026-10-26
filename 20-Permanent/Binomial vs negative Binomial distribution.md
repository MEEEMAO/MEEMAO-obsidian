---
domain: DS
source: Original
tags:
  - discrete-distribution
---
## Binomial distribution
![[Pasted image 20261018000219.png]]
## 核心比較表

| 比較項目                             | 二項分布 (Binomial)                         | 負二項分布 (Negative Binomial)                                    |     |
| -------------------------------- | --------------------------------------- | ------------------------------------------------------------ | --- |
| **固定的條件** (Fixed)                | 總試驗次數 \(n\)                             | 目標成功的次數 \(r\)                                                |     |
| **隨機變數 \(X\)** (Random Variable) | **成功的次數**（在 \(n\) 次試驗中成功了幾次）            | **失敗的次數**（在達到 \(r\) 次成功前，經歷了多少次失敗）  <br>_(或總試驗次數 \(X + r\))_ |     |
| **試驗何時結束**                       | 達到預設的 \(n\) 次後就停止                       | 直到累積到第 \(r\) 次成功才停止（最後一次必定是成功）                               |     |
| **隨機變數範圍**                       | $$(X \in \{0, 1, 2, \dots, n\})（有上下限）$$ | $$(X \in \{0, 1, 2, \dots\})（無上限 / Unbounded）$$              |     |
| **期望值 (Mean)**                   | $$(E(X) = n \cdot p)$$                  | $$(E(X) = \frac{r(1-p)}{p})$$                                |     |
| **變異數 (Variance)**               | $$(Var(X) = n \cdot p \cdot (1-p))$$    | $$(Var(X) = \frac{r(1-p)}{p^2})$$                            |     |

---

## 白話範例對照

以**「投籃」**為例（假設每次投中機率 \(p = 0.5\)）：

- **二項分布（Binomial）情境：**
    
    > 「我今天**固定投 10 次球**（\(n = 10\)），請問我**投進幾球**的機率是多少？」
    > 
    > - 隨機變數是：進球數（0到10之間）。 [[1](https://www.linkedin.com/posts/bruceratner_binomial-distribution-vs-negative-binomial-activity-7456018382304419841-85i6)]
    
- **負二項分布（Negative Binomial）情境：**
    
    > 「我今天一直投，直到**順利投進 5 球**才收工（\(r = 5\)），請問在投進 5 球之前，我**投不進（失敗）了幾球**？」
    > 
    > - 隨機變數是：失敗的次數（可能0次、1次、2次……無限往下）。 [[1](https://zh.wikipedia.org/zh-tw/%E8%B4%9F%E4%BA%8C%E9%A1%B9%E5%88%86%E5%B8%83), [2](https://www.stat.nuk.edu.tw/prost/Web/pdf4.htm)]

## 相關概念
- [[Bernoulli -> binomial -> Poisson distribution]]— Other Discrete data distribution
