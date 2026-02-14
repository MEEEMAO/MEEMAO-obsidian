---
created: 2026-01-15 14:08
type: ML
tags:
  - statistics
aliases:
  - 機率密度函數
  - PDF
---
> [!abstract] 核心一句話
> 連續變數怎麼衡量某個值出現機率?

### 用法:
>**機率密度函數 $f(x)$**: 不直接代表機率，而是代表 x 出現的相對可能

==**PDF 曲線下面積 = 某區間的機率**==
==**PDF 曲線下總面積=1**== ( 機率總和, 所有可能結果發生的機率總和必須是 100% )
![[Pasted image 20260115144214.png]]

---
### **概念**
像「身高」這種可以無限細分的數值時，我們不能問「剛好是 170.0001..cm 的機率是多少」，因為這個機率幾乎趨近於零。但**身高介於170-175** 的機率?

![[Pasted image 20260115150755.png]]
1. **離散化 to 組距**
	- y  is 各組統計數量 or 該組機率
2. **標準化 to 密度**
	- 標準化 - 令所有hist 面積總和 = 1, 寬固定, 轉換高(y, 機率密度值, Density)
	- when 組距很大, Density 分布是階梯; when 組距超小就會是平滑曲線
		![[Pasted image 20260115151837.png]]
	
#### Normal distribution

>![[Normal Distribution.png]]
>y-axis is PDF