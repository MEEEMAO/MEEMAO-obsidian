---
publication_time: "2025-02"
url: https://arxiv.org/abs/2502.05453
keywords: [Generative Agents, Decentralized Control, Knowledge Graph, Large Language Model, Knowledge Graph Memory, Multi-Agent Planning, Structured Communication, Proactivity]
---

# LLM-Powered Decentralized Generative Agents with Adaptive Hierarchical Knowledge Graph for Cooperative Planning

- **Authors**: Hanqing Yang, Jingdi Chen, Marie Siew, Tania Lorido-Botran, Carlee Joe-Wong
- **Venue / Year**: arXiv / OpenReview 2025；GitHub BibTeX 標示為 *The First MARW: Multi-Agent AI in the Real World Workshop at AAAI 2025*
- **Paper URL**: https://arxiv.org/abs/2502.05453
- **Project page**: https://happyeureka.github.io/damcs
- **Code**: https://github.com/13RENDA/Mcrafter_LLM_Agent；legacy environment repo: https://github.com/HappyEureka/mcrafter

> [!summary]
> 這篇提出 **DAMCS**（Decentralized Adaptive Knowledge Graph Memory and Structured Communication System）：每個 LLM-powered agent 各自維護一個 goal-oriented hierarchical knowledge graph memory，並透過 structured communication schema 選擇性分享狀態、資源、目標與求助資訊。核心不是把所有經驗 full broadcast，而是讓 agent 把經驗外部化為可檢索、可摘要、可協作的 memory substrate，再用低冗餘訊息支援 decentralized cooperative planning。

## Research Background

Multi-agent open-world cooperation 需要 agent 在 partial observability、長期任務依賴、資源限制與動態環境下協作。傳統 MARL / CTDE（Centralized Training, Decentralized Execution）雖然能在訓練時學合作策略，但在 open-world survival game 這類任務中會遇到幾個瓶頸：

- **scalability**：agent 數量增加時 centralized training 成本高、策略難泛化。
- **long-term planning**：crafting / resource hierarchy 需要跨多步驟規劃，而 reward shaping 很困難。
- **multi-modal / partial observations**：每個 agent 只看到局部環境，必須融合自己觀察與其他 agent 的訊息。
- **communication overhead**：如果每個 agent 都分享全部歷史經驗，資訊會爆炸；如果完全不溝通，又容易重複工作或搶同一資源。

LLM agents 具有 language reasoning、in-context planning、可解釋決策等優勢，但單純用 prompt 或 free-form communication 仍難處理長期記憶、可控溝通與多 agent task allocation。

## Research Problem

本文要解決的問題是：**在 decentralized、partial observable、open-world multi-agent 環境中，如何讓 LLM agents 不依賴 centralized planner，也能透過 adaptive memory 與 structured communication 進行長期合作規劃？**

更具體地說，作者聚焦四個挑戰：

1. **跨 agent 資訊融合**：每個 agent 只看到局部狀態，需要從自己與他人訊息中建立有用記憶。
2. **動態多模態狀態處理**：agent 需要把 visual / environment observation、inventory、health、location、time、messages 等納入決策。
3. **selective communication**：不是 full sharing，而是只分享對當下協作有用的 resource、goal、request、status。
4. **dependent long-term tasks**：collect diamond 需要依序完成 collect wood → place table → craft pickaxe → collect stone/iron/coal → place furnace → iron pickaxe → collect diamond。

## Method

DAMCS 由三個主要層次構成：

1. **Multi-Agent Crafter (MAC)**：從 Crafter 擴充而來的 open-world multi-agent benchmark。
2. **Adaptive Knowledge Graph Memory System (A-KGMS)**：每個 agent 私有的 hierarchical knowledge graph memory。
3. **Structured Communication System (S-CS)**：用固定 schema 產生、解讀、使用 agent 間協作訊息。

### 1. Problem setting：Dec-POMDP with communication

作者把環境建模成含 communication 的 Dec-POMDP：每個 agent $i$ 只接收自己的 observation $o_i$，並接收其他 agents 透過 message generation function $g_j(o_j)$ 產生的 messages $m_{-i}$。agent policy 不是傳統 neural policy，而是由 MLLM 根據 prompt、working memory、retrieved memory 與 communication messages 產生下一步 action。

### 2. Agent architecture

每個 agent 包含以下模組：

- **Environment / visual input parser**：接收 local observation、面向物件、位置、health stats、inventory、time 等。
- **Short-Term Working Memory (STWM)**：當前 timestep 的決策上下文。
- **Long-Term Memory (LTM)**：experience pool + hierarchical knowledge graph + semantic memory + procedural memory。
- **MLLM planner**：文中實驗使用 GPT-4o via Azure，輸入 STWM 與 prompt，輸出 structured response。
- **Structured output parser**：把 LLM 輸出限制在 schema 中，包含 reflection、goal、action、collaboration。
- **Communication module**：根據 observation、resource status、current goal、assistance request、collaboration schema 產生 message。

### 3. Memory data structures

A-KGMS 把 memory 分成 STWM 與 LTM。

**STWM（pre-stage experience）** 包含：

- **Sensory memory**：raw environment observation、visual input、communication messages。
- **Episodic memory**：agent 的 health、location、time、inventory 等 context。
- **Feedback**：從 semantic / procedural memory 取回的 available actions 與 prerequisites。
- **Retrospection**：從 hierarchical KG 取回的 recent events、achievements、goals、progress。

**LTM（post-stage experience + consolidated graph）** 包含：

- **Experience pool**：每個 timestep 的 post-stage experience，包含 observation、thought、action plan、summary。
- **Semantic memory**：環境中的 factual knowledge，例如 crafting tree 與 action prerequisites；這部分是用 logical expressions explicitly programmed。
- **Procedural memory**：所有可用 actions，例如 move、do、sleep、place table/furnace、make pickaxe、Navigator、share。
- **Goal-oriented hierarchical knowledge graph**：
  - **E nodes**：step-level experiences。
  - **G nodes**：current goals；每個 experience 會連到它正在追求的 goal。
  - **LTG nodes**：long-term goals；由多個 goal nodes 組成，提供長期進度摘要。

圖像上最值得看的是 **Figure 3**：它把 visual input → working memory → MLLM → long-term memory → consolidation → hierarchical KG 的資料流畫得很清楚。Appendix 的 **Figure 7 / 8** 則展示 step node、goal node、long-term goal node 的實際 graph 形態。

### 4. Structured reasoning output

作者用 Pydantic schema 限制 LLM response，避免 free-form text 難以執行。核心欄位有四類：

- **Reflection**：檢查 last action、action result、是否重複、失敗原因與 lesson。
- **Goal**：ultimate goal、long-term goal、current goal、subgoals、progress、status。
- **NextAction**：下一步 action、prerequisites 是否滿足、final action、target material、share item、target agent。
- **Collaboration**：target agent、target need、help method、can help now、being helped by whom、help 會如何改變自己的 plan。

這裡的重點是：LLM 不只輸出「我要做什麼」，而是同時輸出 **為什麼做、目標階層、前置條件、協作對象、是否能幫忙**。這使 agent 的 planning state 可以被寫入 memory，也可以被其他 agent 的 communication 使用。

### 5. Structured Communication System (S-CS)

S-CS 的 message generation module 可表示為：

- 基本訊息：$m_i = g_i(o_i, rs_i, c_i, rq_i)$
  - $o_i$：local observation
  - $rs_i$：resource availability / status
  - $c_i$：short-term goal
  - $rq_i$：assistance request
- 加上 collaboration schema：$m_i = g_i(o_i, rs_i, c_i, rq_i, C_i)$
  - $C_i = \Phi(h_i, I_i, \Delta p_i)$
  - $h_i$：target agent who needs help
  - $I_i$：intention to help
  - $\Delta p_i$：helping others 對自己 current plan 的影響

協作 protocol 採用簡單 hierarchy：agent 1 類似 leader，負責 crafting essential tools；agent $i$ 優先幫助前一個 agent $i-1$，也協助 leader agent。最後一個 agent 會在適當時機切換到 find diamond。**Figure 4** 很適合引用來理解 communication protocol：箭頭代表 agent i 幫 agent j，而 communication flow 反向傳遞需求與狀態。

### 6. Lifecycle / mechanism

可以把 DAMCS agent loop 整理成：

1. **Observe**：agent 接收 local visual/environment input、health/location/time/inventory，以及其他 agents 的 structured messages。
2. **Retrieve**：從 LTM 取回 semantic / procedural feedback，以及 hierarchical KG 中最近 goal node 的 summary、recent experiences、progress。
3. **Use / Plan**：把 STWM + prompt 餵給 MLLM，產生 structured response（reflection、goal、action、collaboration）。
4. **Decide whether to act / help**：NextAction 決定 final action；Collaboration schema 決定是否幫某 agent、分享什麼、會不會改變自己的 plan。
5. **Act / Communicate**：在 MAC 中執行 action，例如 Navigator、collect、craft、share；同時產生 structured communication message。
6. **Write**：把 post-stage experience（LLM thought、action plan、summary、outcome）寫入 experience pool。
7. **Reflect**：Reflection schema 評估 last action result、重複行為、失敗或成功原因。
8. **Consolidate / Update**：每當新 experience 加入，就觸發 consolidation，更新 hierarchical KG：新增 / 連結 E node、G node、LTG node，並更新 goal summary。
9. **Forget / evolve**：文中沒有提出明確 forgetting、pruning 或 conflict-resolution 機制；「adaptive」主要是透過新增經驗、goal linking、summary update，使 graph 隨 episode 進展演化。

## Contribution

1. **DAMCS framework**：提出 decentralized LLM multi-agent cooperative framework，不需要 centralized long-term planner，而是透過每個 agent 的 private A-KGMS + S-CS 達成協作。
2. **A-KGMS memory design**：把 sensory、episodic、semantic、procedural memory 與 goal-oriented hierarchical KG 結合，讓 agent 可以從過去 interaction 中提取與目前 goal 相關的 context。
3. **S-CS structured communication**：用 collaboration schema 讓 agent 選擇性分享資源、需求、目標與協助意圖，降低 full broadcast 的冗餘。
4. **Multi-Agent Crafter benchmark**：擴充 Crafter，使其支援任意數量 agents、independent inventories / observations / health stats、resource sharing、task delegation 與 Gymnasium / PettingZoo 相容接口。
5. **Empirical finding**：在 MAC 中，memory 與 communication 對長期 hierarchical task completion 都有明顯幫助；communication 尤其能減少多 agent 重複工作與資源衝突。

## Conclusion

作者結論是：DAMCS 顯示 LLM agents 若能搭配 hierarchical KG memory 與 structured communication，就能在 open-world multi-agent tasks 中更有效率地進行 long-term planning 與 decentralized cooperation。A-KGMS 幫助 agent 記住 goal progression、避免重複行動；S-CS 讓 agents 能動態分配角色與分享資源，避免大家做同一件事。整體結果支持一個方向：**LLM-driven strategy + external structured memory + selective communication** 是提升 multi-agent cooperation 的可行路徑。

## Reproducibility

- **Paper / PDF**：arXiv 提供完整 PDF 與 HTML；Appendix 包含 structured output schema、adaptive hierarchical KG 範例與完整 six-agent trajectory。
- **Project page**：公開展示 DAMCS、MAC、memory system、RL training、task completion plots 與 multi-agent memory visualization。
- **Code**：project page 連到 `13RENDA/Mcrafter_LLM_Agent`，README 提供 conda environment、`run.py --agent_num 6`、GUI、plot / video generation scripts；另有 `HappyEureka/mcrafter` legacy repo。
- **Model**：實驗使用 GPT-4o via Azure；如果要重現，需要 Azure/OpenAI API access 與相同 prompt / environment config。
- **Evaluation details**：LLM-based results reported over 10 runs；metric 是 Average number of Steps (AS) required for at least one agent to complete each task。
- **Uncertainty**：OpenReview 頁面可讀到 abstract、keywords、TL;DR，但公開頁面本身沒有完整 reviewer decision；workshop venue 主要依 GitHub BibTeX 標示為 AAAI 2025 MARW。

## Evaluation / Benchmark

### Benchmark: Multi-Agent Crafter (MAC)

MAC 是從 Crafter 改造的 scalable cooperative environment：

- 支援 arbitrary number of agents。
- 每個 agent 有 independent observation、inventory、health stats。
- agents 可以 share resources / tools。
- 任務目標明確：越快 collect diamond 越好。
- crafting hierarchy 讓任務自然需要 long-term planning。
- 支援 LLM agents，也相容 RL / MARL frameworks（Gymnasium / PettingZoo）。

### Baselines

- **RL / MARL**：single-agent PPO、multi-agent MADDPG。
- **LLM basic**：structured output prompt + basic memory，只知道 previous actions。
- **LLM Mem**：structured output + A-KGMS memory，無 communication。
- **LLM MemComm**：structured output + A-KGMS memory + S-CS communication，即完整 DAMCS。

### Main results

關鍵結果可參考 **Figure 6** 與 **Table 1**：

- RL / MARL agents 在 1,000 episodes 後 reward 有上升，但遇到 deeper crafting hierarchy 後學習仍很慢。
- Single-agent **LLM Mem** collect diamond 平均 **140.0 ± 35.94 steps**，比 LLM basic 的 **334.67 ± 95.07 steps** 快近 3 倍。
- Two-agent **Mem+Comm** collect diamond 平均 **121.0 ± 30.27 steps**；比 single-agent Mem 少 13.6% steps，也比 LLM basic 少 63% steps。
- Two-agent **Mem without communication** 反而約 **150.4 ± 32.21 steps**，因為 agents 會互搶資源或重複做同一 task。
- Six-agent **Mem+Comm** collect diamond 平均 **85.4 ± 18.04 steps**；比 single-agent Mem 少 39% steps，比 LLM basic 少 74% steps。
- Six-agent **Mem without communication** 約 **140.6 ± 21.89 steps**，幾乎沒有比 single-agent Mem 好，說明「agent 數量增加」本身不等於 cooperation；必須有 communication / role allocation。

### Useful figure/table references

- **Figure 2**：DAMCS framework overview，展示 agents、environment input、communication message、MLLM、working memory、long-term memory 的互動。
- **Figure 3**：memory system，是理解 A-KGMS 的核心圖。
- **Figure 4**：communication protocol，展示 hierarchical helping relation。
- **Table 1**：不同 settings 完成每個 crafting task 的 average steps。
- **Figure 9**：six-agent gameplay 中每個 agent 的 memory graph；可看出 crafting agent 的 sequential goal memory 與 resource-sharing agents 的 clustered memory patterns。

## Borrowable Ideas for Ting's AAAI Direction

1. **Memory as coordination substrate**
   這篇最值得借的是：multi-agent cooperation 不一定要靠 centralized controller；可以讓每個 agent 維持 private memory，再用 structured message 把必要資訊注入彼此的 STWM。這很適合 Ting 的 memory + proactivity 方向：agent 不是被動回覆，而是根據 memory 判斷何時該幫忙、何時該分享。

2. **Goal-oriented hierarchical KG**
   E node / G node / LTG node 的設計很適合用在長期研究助理或 personal assistant：step-level event → current goal → long-term objective。這比純 vector memory 更容易做 progress tracking、反思與「現在應該推哪個目標」。

3. **Structured output = reflection + goal + action + collaboration**
   可以把 agent loop schema 借來設計 Ting 的 AAAI prototype：每次 agent 決策都要輸出 reflection、current goal、next action、help / communication target。這會讓 proactivity 可檢查、可評估，而不是黑箱地「突然主動」。

4. **Selective sharing instead of full broadcast**
   S-CS 的思想適合研究 multi-agent memory sharing：不是同步所有 memory，而是同步「現在對某個 agent 的 goal 有用的 summary / resource / request」。可以轉化成 memory privacy、communication budget、attention bottleneck 的研究問題。

5. **Memory visualization as diagnostic**
   Figure 9 顯示不同角色會形成不同 graph topology。這可以借來做 agent memory analysis：主動型 agent、協助型 agent、監督型 agent 的 memory graph 是否有可觀察差異？能否用 graph metrics 評估 proactivity quality？

6. **Proactivity trigger policy**
   Collaboration schema 中的 `can_help_now`、`target_agent_need`、`change_in_plan` 很像一個可實作的 proactivity trigger：當 memory / observation 顯示某人需要幫助且 agent 有資源，就改變自己的 plan 去協助。

## Attackable Gaps / Follow-up Ideas

1. **Communication protocol 太 hand-crafted**
   agent hierarchy（agent i 幫 i-1 與 leader）很簡單有效，但也把 role allocation 固定住了。後續可研究 learned / adaptive communication topology：誰該幫誰由 memory graph、task dependency、resource distribution 動態決定。

2. **Memory quality 沒有直接評估**
   作者承認 memory quality / adaptability 難量化；實驗主要用 task completion steps 間接證明。可以攻：設計 memory retrieval relevance、graph consolidation correctness、obsolete memory rate、goal-switch accuracy 等 metric。

3. **沒有 explicit forgetting / conflict resolution**
   A-KGMS 會 consolidate and update，但沒有明確處理 outdated facts、conflicting messages、錯誤 reflection、graph bloat。這對長期 assistant 很關鍵，可以做 memory evolution / pruning / contradiction handling。

4. **Semantic memory 是 manually programmed crafting tree**
   semantic memory 提供 accurate prerequisites，但這也降低了 LLM 自主學習世界規則的難度。後續可測：如果 crafting rules 不完整、會變動、或需要從經驗中學，A-KGMS 是否仍有效？

5. **Scalability claims 尚未充分驗證**
   MAC 支援 arbitrary / up to many agents，但主要實驗是 1、2、6 agents，且每組 10 runs。可以擴展到 10、20、50 agents，測 communication cost、token cost、latency、coordination failure modes。

6. **Cost / latency 評估不足**
   Structured output 被宣稱可減少 tokens、提升 generation speed，但主結果主要是 task steps。若走 AAAI，可以補上 token budget、wall-clock latency、message count、LLM calls per success。

7. **Benchmark generality 有限**
   MAC 是 crafting hierarchy，很適合 resource-sharing，但不一定代表 real-world cooperative planning。可設計跨 domain benchmark：research assistant collaboration、calendar/task management、lab experiment planning、financial monitoring 等。

8. **Communication vs memory 的因果拆解仍可更細**
   文中有 Mem vs Mem+Comm ablation，但還可以更細：只 share resource status、只 share goal、只 share request、只 share memory summaries、free-form vs schema、centralized memory vs decentralized memory。

9. **Proactivity safety / interruption cost 沒處理**
   agent 決定幫忙時假設協作都是正收益；真實 personal assistant 需要考慮 interrupt cost、privacy、permission、錯誤主動行為。這是 Ting 的 proactivity 方向可以補上的重要缺口。
