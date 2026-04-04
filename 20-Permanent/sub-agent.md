---
domain: DS
source: https://youtu.be/2rcJdFuNbZQ?si=4H97x7TN6YNC57K2
tags:
  - agent
---

> [!abstract]
> Sub-agent 可減少 main agent 的 context 中不必要的工作過程紀錄，只保留最終結果

## 核心概念

在 Agent 架構中，為了避免 main agent 的 context window 被中間過程佔滿，可以分配子任務給專門的 sub-agent，讓 main agent 只獲得最終摘要或結果。

## 實例：論文比較

假設任務是比較論文 A 與 B 的方法：

**傳統做法（無 sub-agent）**
- Main agent 執行：web search → download → reading → thinking → 產生摘要
- Context 被佔滿：全部工作步驟都留下紀錄

**使用 Sub-agent 的做法**
- **Main agent**：分派任務給 sub-agents，最後接收兩份摘要
- **Sub-agent 1**：讀論文 A 並產生摘要
  - 內部流程：web search → download → reading → thinking → 摘要 A
- **Sub-agent 2**：讀論文 B 並產生摘要
  - 內部流程：web search → download → reading → thinking → 摘要 B
- **結果**：Main agent context 只包含最終的摘要 A 和摘要 B，中間工作步驟被隱藏

![[Pasted image 20260312140547.png|622]]

## 優勢

- 節省 main agent 的 context space
- 中間工作紀錄不會污染主線任務
- 提高並行處理能力和效率

## 相關概念
- [[Context engineering]] — Sub-agent 是優化 context 的策略之一
- [[Skill]] — 與 sub-agent 互補的另一種 context 優化方式