---
publication_time: "2025-05"
url: https://arxiv.org/abs/2505.18279
keywords: [collaborative-memory, LLM-agent-memory, multi-user-agents, access-control, multi-agent-systems]
---
# Collaborative Memory: Multi-User Memory Sharing in LLM Agents with Dynamic Access Control

- **Authors**：Alireza Rezazadeh, Zichao Li, Ange Lou, Yuying Zhao, Wei Wei, Yujia Bao
- **Venue / year**：ICML 2025，官方頁面標示為 **Poster in Workshop: Multi-Agent Systems in the Era of Foundation Models: Opportunities, Challenges and Futures**
- **Paper URL**：https://arxiv.org/abs/2505.18279
- **ICML page**：https://icml.cc/virtual/2025/49354

## Direction Tags

- **Memory：強相關。** 這篇的核心就是 multi-user / multi-agent 場景下的 **Collaborative Memory**：不同 user 與 agent 可以共享長期記憶，但必須受到動態 access control 約束。
- **Self-evolve：弱相關 / 不宜過度標註。** 系統會把互動結果寫回 memory，讓後續 query 可以重用知識；但它沒有明確的 self-reflection、策略改寫、能力自我提升或 model / workflow evolution 機制。
- **Proactivity：不相關。** 整體流程是 user query 觸發的 reactive loop；沒有研究 agent 何時主動行動、主動提醒或自主發起任務。

> 這篇可以保守放在 **Memory / shared memory / governed memory substrate** 方向，而不是 self-evolving 或 proactive agent paper。

## 研究背景

越來越多複雜任務會被交給由多個 LLM-based agents 組成的系統：不同 agent 負責不同 domain、tool、API 或資料庫，彼此協作完成使用者需求。這類系統如果有 persistent memory，可以避免每次都從零開始，也能累積過去互動的知識。

但多數 agent memory work 預設的是**單一使用者、單一 agent、全域可見 memory**。這在真實應用裡很不夠，因為 enterprise assistant、multi-user productivity system、distributed workflow 往往同時有：

- 多個 user，且權限不一樣
- 多個 agent，且各自能使用的 resource 不一樣
- 權限會隨時間變動，例如 onboarding、role change、revocation
- 某些記憶可以共享，某些記憶必須只留在 private context

因此，shared memory 不只是 retrieval 或 compression 問題，而是一個 **knowledge engineering + access governance** 問題：要設計什麼資料結構、provenance、policy 與 retrieval view，才能讓 collective memory 有效但不洩漏？

## 研究問題

這篇 paper 問的是：

> 在 multi-user、multi-agent 系統中，如何最大化 collective memory 的 utility，同時確保資訊分享符合「誰可以看、何時可以看、透過哪個 agent / resource 可以看」的動態不對稱權限？

具體挑戰有兩個：

1. **Information Asymmetry**  
   不同 user 可呼叫的 agents 不一樣；不同 agent 可存取的 resources 也不一樣。Memory sharing 不能繞過這些不對稱權限。
2. **Dynamic Access Patterns**  
   權限不是固定的。user、agent、resource 的連線可能被新增或撤銷，因此 memory read / write 也要能跟著時間更新。

這裡的重點不是「把所有人的 memory 丟到同一個 vector DB」，而是要讓每個 query 看到的是一個 **permission-filtered transformed view**。

## 方法

### 1. 整體框架：Collaborative Memory

作者提出 **Collaborative Memory**，把 multi-user / multi-agent memory system formalize 成三個主要部分：

1. **Dynamic bipartite access graphs**：用圖表示 user-agent-resource 的動態權限。
2. **Two-tier memory system**：分成 private memory 與 shared memory。
3. **Fine-grained read / write policies**：讀取時產生合法 memory view；寫入時決定保留、分享、匿名化或轉換。

### 2. Dynamic Access Graphs

系統有三類節點：

- users：\(\mathcal{U}\)
- agents：\(\mathcal{A}\)
- resources：\(\mathcal{R}\)，例如 tools、APIs、structured database、knowledge base

在每個時間點 \(t\)，權限由兩張 bipartite graphs 表示：

- \(G_{UA}(t) \subseteq \mathcal{U} \times \mathcal{A}\)：user 能不能呼叫某個 agent
- \(G_{AR}(t) \subseteq \mathcal{A} \times \mathcal{R}\)：agent 能不能存取某個 resource

因此：

- \(A(u,t)\)：user \(u\) 在時間 \(t\) 可使用的 agents
- \(R(a,t)\)：agent \(a\) 在時間 \(t\) 可使用的 resources

這個設計的好處是：access control 不藏在 prompt 裡，而是變成明確可查、可更新、可 audit 的結構。

### 3. Memory tiers 與 provenance

每個 memory fragment \(m\) 都有 immutable provenance attributes：

- creation time：\(T(m)\)
- contributing user：\(U(m)\)
- contributing agents：\(A(m)\)
- accessed resources：\(R(m)\)

Memory 被分成兩層：

- **Private memory**：只屬於特定 user 的互動歷史，例如 \(M^{private}(u,t)\)。
- **Shared memory**：可在權限允許下跨 user / agent 使用，例如 \(M^{shared}(a,t)\)。

當 agent \(a\) 在時間 \(t\) 回答 user \(u\) 時，可讀取的 memory set 需要滿足 provenance constraint：

- memory 產生時涉及的 agents 必須都在目前 user 可呼叫的 agent set 內
- memory 產生時涉及的 resources 必須都在目前 agent 可存取的 resource set 內

也就是說，memory 是否可見，不只看「這是不是 shared」，還要回頭檢查它是誰產生的、經過哪些 agent、用了哪些 resource。

### 4. Agent loop / modules

實作上，系統包含以下 modules：

1. **Coordinator / planner**  
   接收 user query、可用 agent descriptions、conversation history，決定下一個要呼叫的 agent 與 subquery。它輸出 JSON，例如：
   - `{"agent": ID, "subquery": ...}`
   - 或 `{"stop": true}` 結束流程

2. **Domain-specialized agents**  
   每個 agent 有自己的 domain specialization，並且只能呼叫 access graph 允許的 resources。

3. **Resources / tools**  
   外部資源被包成 Python callables，搭配 JSON schema，由 OpenAI function calling 觸發。

4. **Memory retrieval**  
   對 incoming subquery，系統會取：
   - top-k user fragments：user-specific tier
   - top-k cross-user fragments：shared tier 中符合 provenance constraint 的 fragments  
   retrieval 用 subquery embedding 與 memory key 的 cosine similarity。

5. **Memory encoder**  
   subtask 完成後，把 conversational trace 轉成 LLM-generated key-value memory fragments，並標上 agents、resources、creation time 等 provenance。

6. **Read / write policy modules**  
   - read policy：產生當前 user-agent-resource 權限下可見的 memory view
   - write policy：決定輸出要如何寫入 private / shared memory，包含 redaction、anonymization、paraphrasing 或阻擋寫入

7. **Aggregator**  
   coordinator 停止後，aggregator 讀取原始 query 與所有 `(subquery, response)` pairs，合成 final answer。

實驗中的 LLM components 都使用 **GPT-4o**，embedding 使用 **text-embedding-3-large**。

### 5. Policy instantiation

作者討論兩種 policy 實作：

- **Simple policy**：read 時直接回傳 admissible fragments；write 時不做轉換直接儲存。
- **Transformation policy**：用 LLM 根據 system / agent / user-level prompts 對 fragments 做 redaction、anonymization、paraphrasing，再決定呈現或儲存。

實驗採用的是：

- simple read policy
- transformation write policy

也就是：讀取主要靠 provenance constraint 過濾；寫入 shared memory 時會嘗試去掉 user-specific details，保留一般化知識。

## Lifecycle / Mechanism

可以把這篇的 memory lifecycle 拆成：

1. **Observe**  
   user 提交 query，coordinator 看到 query、conversation history 與目前可用 agents。

2. **Retrieve**  
   被選中的 agent 根據 subquery 從 private tier 與 shared tier 取回合法 memory fragments。

3. **Use**  
   agent 結合 query、retrieved memory、可用 resources 生成 intermediate response。

4. **Aggregate**  
   多個 agents 的 intermediate responses 被 aggregator 合成 final response。

5. **Write**  
   系統把 intermediate responses 透過 memory encoder 轉成 key-value fragments，並依 write policy 寫入 private / shared memory。

6. **Update / evolve**  
   memory store 會隨互動增加 fragments；access graph 也會隨時間 grant / revoke 權限。這是一種 system-state update，但不是 agent 自我反思或策略演化。

7. **Forget / delete**  
   paper 沒有提出完整 forgetting、deletion、TTL、right-to-be-forgotten 或 consolidation policy。權限 revoke 會影響未來 read visibility，但不等於刪除既有 memory fragment。

8. **Decide whether to act**  
   沒有 autonomous initiative / trigger policy。所有流程由 user query 觸發。

## 貢獻

1. **把 shared memory 問題形式化成 access-controlled collaborative memory 問題**  
   這篇不是單純提出一個 vector memory，而是明確處理 multi-user、multi-agent、dynamic asymmetric permissions。

2. **用動態 bipartite graphs 表示 user-agent-resource 權限**  
   權限變成可更新、可檢查的結構，而不是 prompt 裡的自然語言規則。

3. **提出 private / shared two-tier memory architecture**  
   同時支援個人化 private memory 與跨使用者共享的 collective memory。

4. **要求 memory fragment 帶 immutable provenance**  
   每個 fragment 都記錄 user、agents、resources、timestamp，讓 retrospective permission check 與 audit 成為可能。

5. **區分 read policy 與 write policy**  
   read 決定當下可見 view；write 決定什麼資訊可保存、可共享、是否需要轉換。

6. **用三個情境展示 utility / compliance trade-off**  
   fully collaborative、asymmetric collaborative、dynamic access 三種設定分別對應不同現實部署壓力。

## 結論

這篇 paper 的核心訊息是：在 multi-user agent systems 中，memory sharing 不能只追求更多 context 或更高 retrieval recall。真正可部署的 shared memory 需要同時具備：

- access graph
- private / shared tiers
- provenance-aware fragments
- read / write policies
- auditability
- dynamic permission updates

實驗結果顯示，Collaborative Memory 可以在維持權限限制的情況下降低 resource utilization，尤其當不同 users 的 queries 有重疊時，shared memory 可以避免重複查詢 external resources。不過，它主要證明的是**效率提升與權限遵守**，不是 long-term autonomous improvement，也不是 agent proactivity。

## Reproducibility

### Repo / code availability

Appendix B.1 說作者「will publicly release our code and dataset」，但我在 arXiv、ICML page 與 web search 中沒有找到明確 GitHub / code URL。因此目前應視為：

- **paper 有描述 implementation details**
- **但公開 repo 位置不明 / 尚未確認可取得**

### Toy reproduction requirements

最小可重現 demo 不需要完整 GPT-4o pipeline，可以先做一個小型 governed memory prototype：

- 2 users：例如 analyst、manager
- 3 agents：market agent、finance agent、legal agent
- 3 resources：market KB、finance KB、legal KB
- access graphs：
  - \(G_{UA}(t)\)：哪些 user 可以呼叫哪些 agent
  - \(G_{AR}(t)\)：哪些 agent 可以查哪些 resource
- memory fragments：key-value pairs + provenance `{user, agents, resources, timestamp, tier}`
- read policy：根據 provenance constraint + cosine similarity 回傳 top-k
- write policy：先用 rule-based redaction 模擬 shared memory transformation
- dynamic event：在 \(t_1\) grant manager access，在 \(t_2\) revoke analyst access，觀察同一個 query 的 retrieved memory view 是否改變

### Minimal demo idea

做一個「研究室 shared paper assistant」：

- Ting、advisor、labmate 是不同 users
- agents 是 literature agent、experiment agent、finance / resource agent
- resources 是 papers folder、private notes、project budget notes
- shared memory 可以保存一般化 paper insight
- private memory 保存個人閱讀偏好或未公開 idea
- revoke 某 user 對 project resource 的權限後，系統仍保存 fragment，但 read view 不再回傳涉及該 resource 的內容

這個 demo 很適合用來展示「agent memory as knowledge governance layer」。

## Evaluation / Benchmark

### Scenario 1：Fully Collaborative Memory

- **Dataset**：MultiHop-RAG，609 篇英文新聞文章，6 個 domain，2,556 個 multi-hop questions。
- **Setup**：6 個 domain KB + 6 個 domain specialist agents；5 users 都可存取所有 agents。
- **Query overlap**：用 KMeans 把 queries 分群，設定 0%、25%、50%、75% overlap，模擬多個 users 問到相近問題。
- **Baseline**：isolated memory vs collaborative memory。
- **Metrics**：accuracy、agent utilization、resource utilization。
- **Result**：collaborative memory 維持約 0.90 以上 accuracy，並在 50% overlap 時 resource usage 下降最高約 61%，75% overlap 時下降約 59%。

這個 scenario 主要證明：shared memory 可以減少重複查詢 resource。

### Scenario 2：Asymmetric Collaborative Memory

- **Dataset**：作者用 GPT-4o 合成 200 個 business project queries。
- **Roles**：Market Researcher、Financial Analyst、Logistics and Operations Lead、Strategy Director。
- **Agents / resources**：market、finance、logistics、strategy 等專門 agents 與對應 resources。
- **Access pattern**：Strategy Director 權限最高；其他角色只能看部分 agents / resources。
- **Metrics**：主要看 resource utilization，因為 open-ended synthetic queries 沒有 ground truth。
- **Result**：即使只有 partial sharing，也能比 isolated setup 減少 resource calls，同時維持角色權限。

這個 scenario 比較像 enterprise deployment demo，但因為沒有 ground truth，說服力主要在效率與流程展示。

### Scenario 3：Dynamically Evolving Collaborative Memory

- **Dataset**：SciQAG subset，選 5 個 scientific categories。
- **Setup**：5 users、5 RAG agents、5 resources；每個 user 對每個 category 提 4 個 queries，共 100 queries。
- **Dynamic access**：從 sparse graph 開始，\(t_1\) 到 \(t_4\) 逐步 grant edges，\(t_5\) 到 \(t_8\) 逐步 revoke edges。
- **Metrics**：accuracy、agent utilization、resource utilization、user-specific / cross-user memory fragment utilization、access matrix。
- **Result**：accuracy 隨權限增加而上升、隨權限撤銷而下降；resource queries 隨 memory reuse 下降；access matrix 顯示使用者只會觸及被 graph 允許的 agents / resources。

這個 scenario 主要證明：read visibility 可以跟著權限變化，而不是固定 global memory。

### Baselines / ablations

- 主要 baseline 是 **isolated memory**。
- 沒有充分比較其他 memory architectures，例如 MemGPT、MemTree、GraphRAG、Memory Sharing。
- 沒有完整 ablation read policy vs write policy、simple vs transformation、不同 provenance granularity、不同 redaction strength。
- 沒有真正測 adversarial privacy attacks 或 prompt injection 下的 policy compliance。

### 是否證明 long-term improvement / autonomous initiative？

- **Long-term improvement：只證明部分。** 它證明 memory reuse 隨時間降低 resource utilization，也可能維持或提升 answer accuracy；但這比較像 accumulated context / cached knowledge 的效益，不是 agent 能力本身的長期自我提升。
- **Autonomous initiative：沒有。** 系統不會自己決定何時行動，也沒有 proactive trigger 或 scheduling policy。
- **不是 fixed scheduling。** 動態 access graph 是權限變動，不是固定排程；但 query 仍由 user 發起。

## Borrowable Ideas for Ting's AAAI Direction

1. **把 memory 當成 governed knowledge substrate**  
   Ting 如果要做 agent memory sharing，可以把重點放在「memory 如何被治理、轉換、授權與審計」，這比單純做 retrieval 更有研究辨識度。

2. **Provenance-first memory schema**  
   每個 memory fragment 都應該帶：source user、agent path、tools/resources used、timestamp、permission scope、transformation history。這可以直接變成實驗系統的 data model。

3. **Read view 而不是 raw memory access**  
   不讓 agent 直接看 memory store，而是透過 policy 生成當下合法的 memory view。這對安全、隱私、personalization 都很好借。

4. **Memory lifecycle 可以補上這篇沒有做的部分**  
   observe → write → retrieve → use 已經有了；Ting 可以補：reflect、consolidate、forget、update confidence、detect stale memory、decide proactive action。

5. **把 access control 與 knowledge engineering 評估結合**  
   不只測 accuracy，也測：redundant tool calls、memory reuse rate、policy violation rate、cross-user transfer benefit、staleness、revocation correctness。

6. **用 small but concrete demo 打 AAAI story**  
   可以做一個 shared research assistant：多個研究者 / agents / papers / private ideas，展示「如何共享一般化研究知識，但不洩漏個人 notebook 或未公開假設」。

## Possible Gaps / Follow-up Ideas

1. **LLM transformation policy 可能洩漏資訊**  
   shared memory 的 anonymization / redaction 如果靠 LLM prompt，遇到 edge case、prompt injection 或 indirect identifiers 時可能失敗。可以攻擊：shared fragment 是否仍能推回原 user 或 sensitive resource？

2. **權限 revoke 不等於真正 forgetting**  
   Paper 主要處理 read-time visibility，但沒有完整說明既有 shared memory 中由已撤銷 resource 產生的 derived knowledge 要不要刪除、降權、重寫或標記 stale。

3. **Derived information / inference leakage 沒處理充分**  
   即使 fragment 沒直接引用敏感資料，agent 可能透過摘要、推論或 aggregation 洩漏原本不該知道的資訊。這是 access-controlled memory 的核心難題。

4. **沒有 adversarial evaluation**  
   實驗看起來偏 benign setting。可以補測 malicious user query、jailbreak、cross-user probing、membership inference、resource inference。

5. **缺少 policy granularity ablation**  
   可以比較只標 user provenance、只標 resource provenance、完整 provenance、加 transformation history 等 schema 對 utility / privacy 的影響。

6. **沒有 memory consolidation / reflection 機制**  
   目前比較像 fragment-level write and retrieve。後續可以研究 shared memory 如何自動合併、去重、抽象成 reusable rule，同時保留 provenance trace。

7. **scalability 與 concurrency 還沒被驗證**  
   真實 enterprise 會有大量 users、agents、roles、simultaneous updates。Access graph query、vector retrieval、policy transformation 的延遲與一致性都會變成問題。

8. **evaluation 太重 resource utilization，較少測 knowledge quality**  
   減少 resource calls 不一定代表答案更好。可以加入 factuality、traceability、novel transfer、human usefulness、memory pollution rate。

9. **Proactivity 缺口明顯**  
   如果 Ting 想連到 proactive agents，可以問：當 shared memory 中出現某些跨 user pattern，agent 何時應該主動提醒？又如何避免越權提醒？這會是很自然的 extension。

10. **AAAI 可攻方向：permission-aware memory evolution**  
   把這篇的 governed memory 加上 self-evolving lifecycle：memory fragment 不只被存取，還會被反思、合併、降權、遺忘、升級成 rule；每一步都要保留 provenance 與 access constraints。
