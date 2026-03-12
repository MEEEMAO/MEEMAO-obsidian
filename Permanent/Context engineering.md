---
created: 2026-03-12
type: ML
tags:
  - LLM
  - Agentic
url: https://www.youtube.com/watch?v=2rcJdFuNbZQ&
---
>[! 有效率的運用有限的context window]
>+ 怎麼把 Context 整理好
>+ 怎麼只用少量的 Context 就做到該做的事情

# Openclaw 的核心技術
>讓agent保留context window 給主要任務**

每次query 其實都要附上一堆紀錄:  system prompt & 過去對話，做過甚麼動作...，

# 作法:
## [[sub-agent]]: 減少main agent context中不必要的工作過程紀錄
## [[Skill]]: 有需要才載入任務SOP
