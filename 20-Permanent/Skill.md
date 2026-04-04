---
domain: DS
source: https://youtu.be/2rcJdFuNbZQ?si=4H97x7TN6YNC57K2
tags:
  - agent
---

> [!abstract]
> Skill 是特定任務的 SOP：一段描述執行步驟和所需工具的文字說明

## 核心概念

Skill 是為 agent 定義特定任務的標準操作流程（Standard Operating Procedure），包含：
- **步驟描述**：逐步說明如何完成任務
- **工具清單**：列出執行各步驟所需的工具

![[Pasted image 20260312142954.png]]

## 特點

- **模組化**：每個 skill 是獨立的任務單元
- **按需載入**：Agent 只在需要時才調用相關 skill，不佔用 context
- **可重複使用**：同一個 skill 可被多個 agent 或任務呼叫
- **易於維護**：集中管理，便於更新和迭代

## 相關概念
- [[Context engineering]] — Skill 是優化 context 的策略之一
- [[sub-agent]] — 與 skill 互補的另一種 agent 分工方式