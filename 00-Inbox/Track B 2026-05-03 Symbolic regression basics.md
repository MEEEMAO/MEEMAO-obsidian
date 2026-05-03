# Track B｜Symbolic Regression 是什麼？它和普通迴歸、樹模型差在哪裡？

日期：2026-05-03

## Learning objective

今天的目標是讓 Ting 能用自己的話說明：**symbolic regression 不是先指定模型形式再估參數，而是在「數學表達式空間」裡搜尋一個能解釋資料的公式**。並能區分它和 ordinary regression、decision tree 的核心差異。

## Intuition

一般線性迴歸通常先假設：

\[
y = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \epsilon
\]

接著只估計係數 \(\beta\)。如果真實關係其實是：

\[
y = \sin(2x_1) + 0.5x_2^2 - x_1x_2 + \epsilon
\]

main-effects linear model 會漏掉非線性與交互作用。

Decision tree 則會把特徵空間切成很多區塊，每個區塊給一個平均值。它很會找「哪裡不一樣」，但輸出通常是 piecewise-constant rule，不會直接告訴我們一個平滑公式。

Symbolic regression 的想法是：讓模型搜尋像 `sin(2*x1)`, `x2^2`, `x1*x2` 這類可組合的數學片段，最後得到一個可讀的 expression。它問的是：

> 哪一個公式既能預測，又足夠簡潔、可解釋？

## Method outline

一個簡化版 symbolic-regression pipeline 可以長這樣：

1. 準備候選 expression library：
   - \(x_1, x_2\)
   - \(x_1^2, x_2^2\)
   - \(x_1x_2\)
   - \(\sin(x_1), \sin(2x_1), \cos(x_1)\)
   - \(\log(|x|+1)\)
2. 從空模型開始。
3. 每次加入一個能讓 BIC/AIC 下降最多的 expression term。
4. 停在沒有更好、更簡潔的 term 可以加入時。
5. 用測試集檢查預測，也檢查公式是否接近資料生成機制。

這不是完整 genetic programming symbolic regression，但能展示核心精神：**搜尋 model structure，而不是只搜尋參數**。

## Math / algorithm skeleton

給定候選 expression 集合：

\[
\mathcal{G} = \{g_1(x), g_2(x), \ldots, g_p(x)\}
\]

我們想找一個小子集合 \(S \subset \mathcal{G}\)：

\[
f(x) = \beta_0 + \sum_{g_j \in S} \beta_j g_j(x)
\]

並平衡 fit 與 complexity，例如用 BIC：

\[
\text{BIC}(S) = n \log(\text{RSS}_S/n) + |S|\log(n)
\]

演算法骨架：

```text
S = empty
repeat:
  try adding each candidate g not in S
  fit linear model using S + g
  compute BIC
  add the g with best BIC improvement
until BIC no longer improves
return expression using selected terms
```

## Demo summary

今天的 R demo 模擬：

\[
y = \sin(2x_1) + 0.5x_2^2 - x_1x_2 + \epsilon
\]

比較三種模型：

1. Main-effects linear model：只用 \(x_1 + x_2\)。
2. Decision tree：用 `rpart` 切分特徵空間。
3. Expression-library model：用 greedy BIC 從候選公式片段中挑 term。

實際執行結果中，expression-library model 選到：

```r
y ~ x1_x2 + sin_2x1 + x2_sq
```

係數約為：

```text
x1_x2   ≈ -0.986
sin_2x1 ≈  1.006
x2_sq   ≈  0.518
```

這很接近真實資料生成式：

\[
\sin(2x_1) + 0.5x_2^2 - x_1x_2
\]

測試集 RMSE：

| model | test RMSE |
|---|---:|
| Expression-library model | 0.241 |
| Decision tree | 1.126 |
| Main-effects linear model | 1.506 |

重點不是說 symbolic regression 永遠贏，而是：當真實結構可以用簡潔 expression 表示時，它能給出比普通迴歸更 flexible、比樹更像「公式」的解釋。

## How to run the demo

Remote repo：

```text
/home/langostino/research/symbolic-regression-narm-udrf-demos
```

Run：

```bash
cd /home/langostino/research/symbolic-regression-narm-udrf-demos
/usr/bin/Rscript demos/symbolic_regression_basics.R
```

Output figure：

```text
figures/symbolic_regression_basics_predictions.png
```

## Relation to NARM-UDRF / Deep Rule Forests

這一課是 Track B 的地基：

- Symbolic regression 關心「找公式」。
- Trees / rule forests 關心「找切分、找 subgroup、找 rule」。
- Deep Rule Forests 則把許多 rule 或 tree-based representation 疊起來，形成更豐富的 interaction/subgroup 表示。
- NARM-UDRF 若要發展 contribution，可以思考：是否能把 symbolic expression 的可讀性，和 rule forest 的 subgroup / interaction discovery 能力結合？

也就是說，未來不是只問「哪個模型準」，而是問：

> 我們能不能找出既準確、又能說明 biomedical interaction 或 subgroup heterogeneity 的結構？

## Next questions

1. 如果真實關係是 piecewise formula，而不是單一全域公式，symbolic regression 要怎麼改？
2. PS-Tree 如何把 tree partition 和 symbolic expression 結合？
3. 在 biomedical subgroup analysis 裡，什麼時候 rule 比公式更容易被研究者理解？
