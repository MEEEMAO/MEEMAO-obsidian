---
domain: DS
source: Book-Build a Large Language Model (From Scratch)
tags:
  - llm
---

> [!abstract] GPT 透過 next word prediction 訓練，卻在新任務上展現未被訓練過的能力

## 核心概念

- **架構**：Transformer 的 Decoder 部分
- **訓練方式**：Next word prediction（text completion）

![[Pasted image 20260211123812.png]]

## 湧現行為（Emergent Behavior）

> The ability to perform tasks that the model wasn't explicitly trained to perform

只是被訓練來預測下一個 word，卻在新任務上 zero-shot / few-shot 也有不錯表現。

## 相關概念
- [[Transformer 用 Attention 機制取代 RNN 的全記憶方式]] — GPT 的基礎架構
- [[BERT 用雙向 Encoder 理解上下文語義]] — 同為 Transformer 變體，但用 Encoder 且雙向
- [[Pre-training 讓 LLM 學會語言基礎能力]] — GPT 的預訓練階段
