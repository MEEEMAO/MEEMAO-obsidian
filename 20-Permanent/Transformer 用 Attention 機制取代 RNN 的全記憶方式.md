---
domain: DS
source: Book-Build a Large Language Model (From Scratch)
tags:
  - llm
---

> [!abstract] Attention 機制讓模型只記住重要的過去，而非 RNN 的全部記憶

## 核心概念

RNN 的問題：試圖記住所有過去的資訊，隨序列變長效果遞減。

Transformer 的解法：**Attention 機制只記住重要的過去**

![[Pasted image 20260211123548.png]]

## 架構

- **Encoder**：將輸入序列轉成向量表示
- **Decoder**：根據 Encoder 的表示生成輸出序列
- 原始設計：專為語言翻譯的 seq2seq 任務

## 相關概念
- [[BERT 用雙向 Encoder 理解上下文語義]] — Transformer 的 Encoder 變體
- [[GPT 用 Decoder 做文字生成並展現湧現能力]] — Transformer 的 Decoder 變體
