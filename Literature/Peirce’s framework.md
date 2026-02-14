---
created: 2026-01-19 13:50
type: ML
tags:
  - Hypothesis-generation
aliases: []
---
> [!abstract] 
> 用於 Hypothesis discovery的架構, 將過程分為Abduction, Deduction & Induction 三個互相關聯的推理階段

1. forming an explanatory hypothesis to explain observations through **abduction**
2. proceeds with iteratively apply hypothesis to solve problem or derive new knowledge with **deduction**
3. validate hypothesis through **induction**

![[Pasted image 20260119155059.png]]
##### Abduction: generate
+ generates explanatory hypotheses based on an initial set of observations. 
+ only form that generates entirely new ideas or explanations
##### Deduction: apply
+ apply these hypotheses and derive predictions. 
##### Induction: validate & refine
+ Validate: 這個假設的正確程度 - evaluates how well the predicted observations align with actual outcomes.
+ Refine: updating the confidence of the hypotheses or rejecting those that are no longer valid.