---
domain: DS
source: https://youtu.be/2rcJdFuNbZQ?si=4H97x7TN6YNC57K2
tags:
  - agent
---

> [!abstract]
> Context engineering 是有效率地運用有限的 context window 的技術，包括組織 context 結構和用少量資訊完成複雜任務

## 核心挑戰

每次 query 都需要附上大量紀錄：
- System prompt
- 過去對話歷史
- 執行過的動作記錄
- ...

這會快速佔滿有限的 context window，導致能夠處理的新任務空間受限。

## 解決方案

### Openclaw 的核心技術

讓 agent 保留 context window 給主要任務，而不被瑣碎工作過程佔滿。

## 實踐策略

### 1. [[sub-agent]] — 減少中間過程紀錄

將子任務分派給專門的 sub-agent 執行，main agent 只接收最終結果，中間工作步驟不進入 main agent 的 context。

### 2. [[Skill]] — 按需載入任務 SOP

將任務標準操作流程（SOP）定義為 skill，只在需要時才載入，不預先佔用 context。

## 相關概念
- [[sub-agent]] — 分工策略，減少中間過程
- [[Skill]] — 模組化策略，按需載入任務說明
