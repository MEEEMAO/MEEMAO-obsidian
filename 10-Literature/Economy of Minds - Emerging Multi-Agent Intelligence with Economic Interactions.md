---
publication_time: 2026-10
url: https://zhentingqi.github.io/internal/projects/EoM/
keywords:
  - Agent-swarms
---
> [!info] 
> 讓一群能力有限的 LLM agent 用經濟機制自己分工 (auction,exchange,rewards) ,不需要中央調度或預設流程。賺錢的 agent 被複製變異, 賠到破產的被淘汰,整個 agent 群像會演化的市場。
## 1. 研究問題
	沒有中央控制時,一群 agent 能否自主決定誰該行動 & 自我演化,變成比單一強 agent 更強的集體智慧?
	How can a population of agents self-orchestrate and self-adapt into stronger collective intelligence without centralized control?
---
## 2. 方法
- Agent = 同一個凍結的 LLM + 不同 system prompt, 各有喚醒條件、固定 bid、財富值。
	- prompt 內容: 
		- **喚醒**: 看當前狀態, 回答「現在該不該輪到我」,輸出是或否。
		- **行動**:被選中後,說明這個角色要做什麼、怎麼做。
- 誰做 by auction: 每步由被喚醒者中 bid 最高的行動。
![[Pasted image 20261109113101.png|714]]
- Transaction: 贏家付 bid 給上一位行動者,並拿走環境報酬,價值因此往前流動(bucket brigade)。
![[Pasted image 20261109113245.png|695]]
- 跨 episode: 扣租金、產生新 agent。
	- 破產者移除+突變/ 最有錢的繁衍
	- Exploration: 根據破產者修正&產生不同方向的新 agent。這樣失敗會變成新嘗試的依據,也避免整個族群只剩同一套做法
	- Exploitation: 複製、微調最富有的parent, 慢慢累積成更強的專才
- Bid 不學習,新人設為對手最高 bid + ε , 這樣新加入agent才至少有action機會
---
## 3. 實驗做法
- A _partial agent_ is intentionally incomplete: a restricted action space, access to one tool, a short generation budget, a specialized role, or partial observation. 
- A _complete agent_ has the full task interface and attempts the task end-to-end.
- partial agent EOM 合作 vs complete agent 自己 end-to-end
## 4. 貢獻
- 提出用經濟機制讓 LLM agent 群自組織,不需預設拓樸。
- 五個任務(數學、金融、科研、晶片設計、系統優化)優於單一 agent 與其他 baseline,例如 MATH 的 Llama-3.1-8B 從 15.9% 到 57.0%。
- 消融顯示拍賣、exploration、exploitation 都有貢獻; 案例顯示演化出可重用的推理流程,以及專才勝過通才。
