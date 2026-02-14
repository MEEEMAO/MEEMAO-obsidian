---
created: 2026-01-25 22:22
type: ML
tags:
  - statistics
aliases: []
---
>**在某一個連續變數(ex. time)上, 有「異常聚集」（Clusters）?**

- Sliding window: 沿著連續變數滑動
	- try different window size
	- 每次scan all data 後會有一個max LR

> H0: 沒有異常群聚，窗內窗外一樣，共用一個global Poisson($\lambda_0$)分布
>H1: 有異常群聚，窗內窗外不同，各自一個 [[離散分布#Poisson 分布 - 連續型單位]]($\lambda_1,\lambda_2$)分布

- Example: 
	- global 情況 : 100 case/1000 exposure
	- win內: 10/200, win外: 90/800

- **LR - Likelihood ratio: L(H1)/L(H0)。*H1這個假設更符合資料?***
	- L(H0): 共用一個 global 分布，產生這組資料的機率?
		==用global ratio 計算: win內資料出現機率 x win外資料出現機率==
		1. win內期望次數=$200*100/1000$=20 
		2. win外期望次數=$800*100/1000$=80
		3. win內資料出現機率:$P(win_內)=P(K_{in}​=10∣μ_{in}​=20)$
			Poisson分布在平均=20下, 出現K=10的機率
		4.  win外資料出現機率:$P(win_外)=P(K_{in}​=90∣μ_{in}​=80)$`
			Poisson分布在平均=80下, 出現K=90的機率
		5. $L(H0)=P(win_內)*P(win_外)$
	- L(H1): win內&win外各自一個Poisson分布，產生這組資料的機率?
		==win內資料出現機率 x win外資料出現機率==
		1. win內Poisson $\lambda_1$ = 10 (期望次數:$ratio*exposure$)
		2. win外Poisson $\lambda_2$ = 90 (期望次數:$ratio*exposure$)
		3. win內資料出現機率:$P(win_內)=P(K_{in}​=10∣μ_{in}​=10)$
			Poisson分布在平均=10下, 出現K=10的機率
		4.  win外資料出現機率:$P(win_外)=P(K_{in}​=90∣μ_{in}​=90)$`
			Poisson分布在平均=90下, 出現K=90的機率
		5. $L(H1)=P(win_內)*P(win_外)$
- 每次滑動都計算LR, 找 max LR(這一段群聚性最強)
- **統計顯著性**
	- 隨機排序case位置, find max LR of all windows
	- 重複多次 like 999
	- 加上真實資料的max LR 共有1000個值進行排序, 如果真實max LR 在前5%代表p-value<0.05 (真的罕見,不是巧合)