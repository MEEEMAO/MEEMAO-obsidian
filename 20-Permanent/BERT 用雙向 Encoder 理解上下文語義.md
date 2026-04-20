---
domain: DS
source: Book-Build a Large Language Model (From Scratch)
tags:
  - llm
---
> [!abstract] BERT 透過雙向訓練理解句子的完整上下文，擅長文本分類任務

## 核心概念

- **架構**：Transformer 的 Encoder 部分
- **訓練方式**：Masked word prediction — 遮掩句中某個詞，根據前後文預測
- **雙向（bidirectional）**：同時看前面和後面的上下文

## 強項：Text Classification

根據整句話的前後部分來預測被遮掩的詞，因此能更好地理解句子的上下文。雙向訓練使 BERT 更適合處理需要全局上下文理解的任務，如文檔分類、情感預測。

## 相關概念
- [[Transformer 用 Attention 機制取代 RNN 的全記憶方式]] — BERT 的基礎架構
- [[GPT 用 Decoder 做文字生成並展現湧現能力]] — 同為 Transformer 變體，但用 Decoder 且單向
