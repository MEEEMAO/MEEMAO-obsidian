---
publication_time: 2026-10
url: https://zhentingqi.github.io/internal/projects/EoM/
keywords:
  - Agent-swarms
---
> [!info] 
> 讓一群能力有限的 LLM agent 用「拍賣 + 付錢 + 賺賠」的經濟機制自己分工,不需要中央調度或預設流程。賺錢的 agent 被複製變異,賠到破產的被淘汰,整個 agent 群像會演化的市場。
## 1. 研究問題
	沒有中央控制時,一群 agent 能否自我編排(誰何時行動)並自我演化,變成比單一強 agent 更強的集體智慧?
---
## 2. 方法
- Agent = 同一個凍結的 LLM + 不同 system prompt, 各有喚醒條件、固定 bid、財富值。
- 每步由被喚醒者中 bid 最高的行動。贏家付 bid 給上一位行動者,並拿走環境報酬,價值因此往前流動(bucket brigade)。
- 跨 episode:扣租金、破產者移除,再以 prompt 變異(複製富者)或修正(補破產者)產生新 agent。
- Bid 不學習,新人設為對手最高 bid + ε 後凍結。演化只發生在 prompt 空間,權重不動。
---
## 3. 貢獻
- 提出用經濟機制讓 LLM agent 群自組織,不需預設拓樸。
- 五個任務(數學、金融、科研、晶片設計、系統優化)優於單一 agent 與其他 baseline,例如 MATH 的 Llama-3.1-8B 從 15.9% 到 57.0%。
- 消融顯示拍賣、exploration、exploitation 都有貢獻; 案例顯示演化出可重用的推理流程,以及專才勝過通才。
