---
domain: DS
source: Book-Build a Large Language Model (From Scratch)
tags:
  - llm
---

> [!abstract] Pre-training 階段讓模型學習語法、詞彙、語言結構，具備預測下一個 token 的能力

![[Pasted image 20260305140514.png]]

## 核心概念

學習語言的基礎能力，包括語法、詞匯、語言結構，可以相對準確地預測下一個 token。

![[Pasted image 20260211115710.png]]

## Few-shot Capabilities

> Learn to perform new tasks based on only a few examples instead of needing extensive training data

預訓練後的模型已具備在少量範例下執行新任務的能力。

## 相關概念
- [[Fine-tuning 讓 LLM 適應特定任務]] — 預訓練之後的下一階段
- [[GPT 用 Decoder 做文字生成並展現湧現能力]] — Pre-training 產出的基礎模型
