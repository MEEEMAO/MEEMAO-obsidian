---
created: 2026-03-12
type: ML
tags:
  - LLM
  - Agentic
url: https://www.youtube.com/watch?v=2rcJdFuNbZQ&
---
>可以減少main agent context中不必要的工作過程紀錄

~~~
ex. 比較論文A & B 中的方法
	- 大龍蝦: subagent 1(讀論文A並摘要)，subagent 2(讀論文B並摘要)
	- subagent 1 : web search, download, thinking....最後得到摘要A
	- subagent 2 : web search, download, thinking....最後得到摘要B
大龍蝦context可以直接取得摘要
~~~
![[Pasted image 20260312140547.png|622]]