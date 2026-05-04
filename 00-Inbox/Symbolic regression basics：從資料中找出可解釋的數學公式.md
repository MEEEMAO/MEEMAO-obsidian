---
domain: DS
source: Langostino
tags:
  - symbolic-regression
  - interpretable-ml
  - regression
---

# Symbolic regression basics：從資料中找出可解釋的數學公式

> [!summary] 一句話
> **Symbolic regression（符號迴歸）**不是只替固定模型調參數，而是嘗試從資料中同時找出「公式的形狀」與「公式中的係數」，讓模型可以用簡潔的數學式被人理解、檢查與推論。

## 學習目標

讀完這張筆記後，應該能夠：

1. 說明 symbolic regression 想解的問題是什麼。
2. 分辨 ordinary regression、decision tree 與 symbolic regression 的差異。
3. 用一個小例子理解「從候選運算與變數中搜尋公式」的直覺。
4. 說出 symbolic regression 的優點、限制與適合使用的情境。

## 基本直覺：模型不只是黑盒，也可以是一條公式

假設我們觀察到一組資料：

\[
(x_1, x_2) \rightarrow y
\]

一般機器學習常見目標是找到一個預測函數：

\[
\hat{y} = f(x_1, x_2)
\]

差別在於：**我們是否事先指定了 \(f\) 的形式？**

- ordinary regression 通常先指定形式，例如線性模型：
  \[
  \hat{y}=\beta_0+\beta_1x_1+\beta_2x_2
  \]
- decision tree 以一系列 if-then 規則切分資料空間。
- symbolic regression 則嘗試從候選元素中搜尋公式，例如：
  \[
  \{x_1, x_2, +, -, \times, \div, \sin, \log, \sqrt{\cdot}\}
  \]
  然後自動組合出類似：
  \[
  \hat{y}=1.8\sin(x_1)+0.5x_2^2
  \]

換句話說，symbolic regression 的核心問題是：

> 在預測準確度、公式簡潔度與可解釋性之間，搜尋一個好的數學表達式。

## 與普通迴歸的差異

| 方法 | 事先假設 | 學到的是什麼 | 可解釋性 | 主要限制 |
|---|---|---|---|---|
| Ordinary regression | 公式形式通常先固定 | 係數 \(\beta\) | 高，若形式合適 | 若真實關係非線性，可能欠擬合 |
| Decision tree | 用切分規則近似關係 | if-then 分割規則 | 中等，淺樹易讀 | 預測通常是階梯狀，外推能力弱 |
| Symbolic regression | 只給變數、運算子與複雜度限制 | 公式結構 + 係數 | 高，若公式簡潔 | 搜尋空間大，容易過度複雜 |

## 小例子：如果真實世界有一條隱藏公式

假設資料其實來自：

\[
y = 2\sin(x) + 0.3x^2 + \epsilon
\]

其中 \(\epsilon\) 是雜訊。

不同方法會有不同表現：

1. **線性迴歸**可能只能學到：
   \[
   \hat{y}=a+bx
   \]
   它簡單、穩定，但無法描述 \(\sin(x)\) 與 \(x^2\) 的彎曲型態。
2. **決策樹**可以用許多區間切分逼近曲線，但模型長得像「階梯」，較難表達背後的連續公式。
3. **Symbolic regression**若候選庫中包含 \(\sin(x)\) 與 \(x^2\)，就可能找出：
   \[
   \hat{y}\approx 2\sin(x)+0.3x^2
   \]
   這不只可以預測，也讓人看見資料中的結構。

## Symbolic regression 通常怎麼搜尋公式？

實務上常見作法包含：

- **Genetic programming**：把公式當成樹狀結構，透過變異、交配與選擇逐代改進。
- **Expression library / basis search**：先建立一批候選特徵，例如 \(x\)、\(x^2\)、\(\sin(x)\)、\(\log(x)\)，再用稀疏或一般迴歸挑出有用項。
- **Neural-guided search**：用神經網路或大型模型輔助探索可能的公式結構。

這張入門筆記先抓住最重要的觀念：**symbolic regression 是在「公式空間」中做模型搜尋。**

## 什麼時候適合用？

適合：

- 科學建模：想從實驗資料找出可讀的定律或近似關係。
- 工程建模：需要一個能部署、能檢查、能外推的簡潔公式。
- 教學與分析：想讓模型結果能被人討論，而不只是拿到預測分數。

要小心：

- 如果候選運算子太多，搜尋空間會爆炸。
- 若只追求訓練誤差，可能得到很長但不可解釋的公式。
- 找到的公式不一定是真實因果，只是資料與假設下的好近似。

> [!tip] 實務準則
> 好的 symbolic regression 不只看 RMSE，也要同時看公式長度、穩定性、是否符合領域知識，以及在測試資料或外推區間的表現。

## 清楚 takeaway

**Ordinary regression 是「先選公式，再估係數」；decision tree 是「用規則切資料空間」；symbolic regression 則是「讓資料幫我們搜尋可解釋的公式」。**

當目標不只是預測，而是理解資料背後可能的數學結構時，symbolic regression 是很有價值的工具。不過它不是魔法：公式是否可信，仍需要測試資料、領域知識與簡潔度約束一起檢查。

## 正確性檢查備註

- 已用公開資料交叉確認 symbolic regression 的核心定位：從資料學習簡潔、可解釋的數學表達式；傳統方法常包含 genetic programming，近年也有 neural-guided / data-driven model discovery 方向。
- 參考線索：*Interpretable Scientific Discovery with Symbolic Regression: a Review*（arXiv:2211.10873 / Springer review）。
