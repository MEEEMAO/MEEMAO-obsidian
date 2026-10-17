---
domain: DS
source: Original
tags:
  - discrete-distribution
---
### 一句話區分

- **Bernoulli：** 只做 **1 次**，結果二選一（有 / 沒有）。
- **Binomial：** 把 Bernoulli **重複 n 次**，數「總共發生幾次」。
- **Poisson：** 沒有固定次數，在一段**時間或空間**內數「事件發生幾次」。

### 各自適用情況

|      | Bernoulli             | Binomial                 | Poisson     |
| :--- | --------------------- | ------------------------ | ----------- |
| 在算什麼 | 單次有沒有發生               | n 次中發生幾次                 | 一段時間內發生幾次   |
| 機會次數 | 1 次                   | 固定 n 次                   | 沒有上限        |
| 參數   | p（發生機率）               | n、p                      | λ（平均發生次數）   |
| 平均   | p                     | np                       | λ           |
| 變異   | p(1−p)                | np(1−p)                  | λ           |
| 例子   | 這一次透析有沒有 complication | 100 次透析中有幾次 complication | 每小時有幾通電話打進來 |

### 三者的關係

**1. Bernoulli 是 Binomial 的特例。**  
Binomial 就是把 n 個獨立的 Bernoulli 加起來。n = 1 時，binomial 就等於 Bernoulli。

**2. Poisson 是 Binomial 的近似。**  
當 **n 很大、p 很小**時，binomial(n, p) 會非常接近 Poisson(λ = np)。直覺是：機會很多，但每次發生的機率很低，這時「機會有上限」這件事幾乎不影響結果。

觀察一下變異就看得出來：binomial 是 np(1−p)，p 很小時 (1−p) ≈ 1，就變成 np = λ，跟 Poisson 一樣。

```
Bernoulli ──重複 n 次──> Binomial ──n 大、p 小──> Poisson
```
![[Pasted image 20261017232121.png]]

## 相關概念
- [[Probability Density Function(PDF)-機率密度函數]] — continuous data 的機率分布
