

> 主題：形式化儲存過去知識，在**整理**與**檢索**上的好處
> 範圍：ILP 遺忘／重構 → 程式庫學習 → LLM skill 庫 → agent 記憶整併 → 批判性評估
> 檢索方式：arXiv API 關鍵字檢索（2026-10-29）；**未涵蓋 Google Scholar／會議論文集**，不能保證完整。
> 核對程度：【全文】＝讀過全文相關段落；【摘要】＝僅讀摘要（數字取自摘要，未對照內文）。

---

## 0. 一句話結論

- **沒有找到**同時具備「LLM 發明、執行驗證、保語意整理（去重＋包含／依賴結構）、agent 結構化檢索」四項的工作（限於我的檢索範圍，不寫「首個」）。
- **最接近的對手是 SkillDAG（2026）**：同樣把結構開成 agent 可呼叫的介面。你的差異在「結構的來源」——由可執行語意確定性推得，而非 LLM 分類。
- **最需要防守的風險來自批判性文獻**：LLM 函式庫的「重用」常常極少，且成本對齊後增益會消失。**重用率與成本對齊 baseline 應列為必做。**

---

## 1. 研究地圖

| 研究線 | 代表作 | 整理什麼 | 正確性／檢索 | 對 ADVENT |
|---|---|---|---|---|
| ILP 遺忘／重構 | Forgetgol（2019）、Knorf（2020）、Scalable Knorf（2024） | 邏輯程式的子程式：遺忘、壓縮 | 符號式、保語意；無 LLM、無檢索 | C2 的直系祖先 |
| 程式庫學習 | DreamCoder、Stitch、LILO、ReGAL | 函式庫壓縮（MDL）、LLM 命名／重構 | 執行驗證；檢索多為整包放 prompt | 命名消融（LILO） |
| LLM skill 庫 | Voyager、SkillX、SkillsVote、TrackRL、SkillAxe、**SkillDAG**、Executability Gating | 生命週期、去重、型別化關係圖 | 結果導向／LLM 判斷；SkillDAG 以 agent 工具暴露結構 | **C3 最接近** |
| Agent 記憶整併 | Dual-Layer、MemForest、LAM | 寫入過濾、合併、有損壓縮 | 經驗指標；LAM 給檢索分數誤差界 | 「整理不傷檢索」的敘事 |
| 批判性評估 | Berlot-Attwell（2024、2025） | LEGO-Prover、TroVE | 重用極少；成本對齊後增益消失 | **必做的評估補強** |

---

## 2. 逐篇摘要（2024–2026，依相關度排序）

### 2.1 SkillDAG: Self-Evolving Typed Skill Graphs for LLM Skill Selection at Scale（2026，arXiv 2606.03056）【全文：方法與主要結果段落】
- **問題**：skill 庫變大後，「哪些 skill 進 prompt」成為瓶頸；top-K 向量檢索把 skill 當獨立文件，忽略依賴、衝突、特化、重複等關係。
- **方法**：建立型別化 skill 圖（冷啟動：雙視角 embedding 篩候選 → **LLM pair classifier 判邊型**；`conflicts_with` 只由線上執行證據產生）。圖以 agent 可呼叫的介面提供（search 回傳向量匹配＋型別鄰居＋衝突訊號；show 取內容）；agent 可用 propose-then-commit 新增「執行佐證」的邊，須通過無環與不矛盾檢查，且 append-only 可回滾。
- **結果（論文自述）**：兩個 benchmark、三個模型家族；ALFWorld 成功率最高 +14.3 點；Ret@5 在 10 倍 skill 池下維持 78.2；部分設定下在接近天花板時與 baseline 持平。
- **我的看法**：
  - 設計哲學與你的 M3 幾乎一致（「結構給 agent 查，而不是被固定管線吃掉」）→ C3 不能宣稱這個設計是新的。
  - 弱點正好是你的優勢：邊靠 LLM 分類，沒有語意保證；skill 是文字，無法試跑。你的邊由 fingerprint 與 body 解析確定性推得，且 `test_rule`／`deduce` 可直接檢驗候選。
  - 領域不同（ALFWorld vs ILP），**數字不可直接比較**。

### 2.2 LLM Library Learning Fails: A LEGO-Prover Case Study（2025，arXiv 2504.03048）；前作 Library Learning Doesn't: The Curious Case of the Single-Use "Library"（2024，arXiv 2410.20274）【全文：摘要與關鍵表格】
- **問題**：LLM 函式庫學習系統宣稱靠重用提升表現，是真的嗎？
- **方法**：分析 LEGO-Prover（Isabelle 引理）與 TroVE（Python 函式）的行為與消融。
- **發現（論文自述）**：直接重用極少（前作 Table 1：引理幾乎不重複被用）；進步主要來自自我修正與自我一致性；2025 論文進一步指出**扣除運算成本後，LEGO-Prover 並未優於單純提示的 baseline**，並呼籲以行為分析與**同等運算預算的 baseline** 作為評估標準。
- **我的看法**：對你是最重要的一組文獻。KANDY 若 Easy→Hard 的 PI 不共用，C3 與 RQ2 都會被這類質疑擊中。你的 KP 是邏輯 predicate，重用可以直接計數，比自然語言 skill 容易回應——**這是機會**。

### 2.3 Don't Offer What Can't Be Done: Deterministic Executability Gating for LLM Skill Selection at Scale（2026，arXiv 2608.01050）【摘要】
- **問題**：skill 與使用者訊息語意相關，卻可能在當前帳戶狀態下無法執行。
- **方法**：Wix 客服的三階段管線——語意比對 → **確定性可執行性閘門**（閘門與 skill 共用同一組狀態 predicate）→ LLM 選擇。
- **結果（摘要）**：756.6K 則訊息的上線分析中，閘門在已匹配的流量裡移除 59.4% 的 skill–訊息配對（約 59.1% 的 skill 說明 token）。
- **我的看法**：與你的方向同源——「用可執行的 predicate 取代 LLM 判斷」。可借用的想法：search agent 在建議某 predicate 前，先在當前題目樣本上**試跑過濾**（你的 `test_rule` 已具備）。成果是工業部署報告，非 ILP 場景。

### 2.4 SkillsVote: Lifecycle Governance of Agent Skills（2026，arXiv 2605.18401）【摘要】
- **方法**：skill 的蒐集、推薦、歸因、演化全生命週期治理；僅允許「成功且可重用」的發現經**證據閘門**更新庫；agentic library search。在 Terminal-Bench 2.0、SWE-Bench Pro 上提升表現。
- **我的看法**：與 C1+C2 的「閘門＋管理」理念相近，但閘門依據是任務結果與 LLM 歸因；你的依據是 Prolog 執行與統計檢定。可引用為「LLM 端的治理做法」。

### 2.5 SkillX: Automatically Constructing Skill Knowledge Bases for Agents（2026，arXiv 2604.04804）【摘要】
- **方法**：把軌跡蒸餾為三層技能（策略計畫／功能技能／原子技能）；依執行回饋反覆修正；主動探索擴充；可跨 agent 與環境重用。
- **我的看法**：層級結構類似你的「依賴邊」，但層級是設計出來的，不是由語意推出。可引用其「執行回饋迭代修正」對照 M1b。

### 2.6 Trackable Skill Library Lifecycle Management for Agentic RL（TrackRL，2026，arXiv 2610.25126）【摘要】
- **方法**：在 GRPO 群組內隨機注入 skill，形成線上隨機對照實驗，估計每個 skill 的**因果貢獻**；以證據驅動的狀態轉移管理生成→納入→保留／淘汰，並有容量上限。
- **我的看法**：你的 R0–R3 只看**語意**（重複、有效），不看**效用**。TrackRL 提示「去重後仍可能有無用或有害的 predicate」——可列為 future work（效用導向的淘汰）。

### 2.7 SkillAxe: Sharpening LLM-Authored Agent Skills Through Evaluation-Guided Self-Refinement（2026，arXiv 2606.10546）【摘要】
- **發現（摘要）**：SkillsBench 上，人寫 skill 提升通過率 16.2 個百分點，**LLM 自己寫的 skill 沒有可測得的增益**；其方法可補回 47–67% 的差距。
- **我的看法**：支持 C1 的立場——LLM 的發明不能直接信任，要有驗證閘門。可作為「為什麼需要 RC＋Prolog 回饋」的外部佐證（領域不同，措辭須保守）。

### 2.8 記憶整併三篇（2026）【摘要】
| 論文 | 做法 | 摘要中的數字 |
|---|---|---|
| Dual-Layer Agentic Memory（2608.22215） | 寫入階段路由（不寫／新增／更新），小→大模型級聯；定期把高價值記憶固化進參數 | 刪除最多 68% 冗餘記憶，保留 >98% 的 QA EM |
| MemForest（2609.08273） | 事件樹分區＋漸進合併；錨點引導的檢索 | 壓縮 50% 記憶，保留 97.1%（unimodal）；檢索 1.89× 加速 |
| LAM（2609.32256） | **確定性**去重規則＋檢索分數擾動的**可證上界**；作者強調這是分數擾動界，不是排名不變的保證 | 移除 22.47% 觀察 token，保留 99.984% 的 gold-patch 證據 |

- **我的看法**：Dual-Layer 與 MemForest 都靠經驗指標說明「整理沒傷害」；**LAM 最值得學**——它同時要求確定性規則與可度量的檢索影響。你可以對 R0–R3 報告同類指標：整理前後，既有任務的**可解率／檢索命中是否不變**。

### 2.9 ILP 側的近期工作【摘要；僅作定位】
- **Scalable Knowledge Refactoring using Constrained Optimisation（2024，2408.11530）**：以約束最佳化擴展邏輯程式重構，在某些情境比前人快、壓縮更多（摘要稱有時達 60%）。→ C2 的符號式對照；但它重構的是**已學得的程式**，並非 LLM 發明的 predicate，也沒有檢索層。
- **Predicate Renaming via LLMs（2025，2510.25517）**：用 LLM 為 PI 產生的無名 predicate 命名；僅在少量手工規則上評估。→ 可直接支持命名消融。
- **Hypothesis Generation via LLM-Automated Language Bias for ILP（2025，2505.21486）**、**ILP-CoT（2509.21874）**、**UniPred（2512.17992）**、**VisualPredicator（2410.23156）**：LLM 提供語言偏誤／規則結構／predicate 提案，再由符號系統或學習模組接手。→ 與「LLM 發明、形式化把關」同一路線，但都沒有處理**發明後的 predicate 管理與檢索**（依摘要判斷）。

---

## 3. 與 ADVENT 的比較（我的主觀判斷）

| 工作 | 儲存單位可執行／可驗證 | 去重／重構 | 檢索結構 | LLM 發明 | 驗證方式 |
|---|---|---|---|---|---|
| Forgetgol / Knorf | ✔ 邏輯程式 | ✔ 遺忘／重構（符號） | ✘ | ✘ | 符號式 |
| DreamCoder / LILO / ReGAL | ✔ 程式 | ✔ 壓縮（LILO、ReGAL 用 LLM） | △ 多為整包放入 | △（LILO、ReGAL） | 執行 |
| SkillX / SkillsVote / TrackRL | △ 文字＋腳本 | △ 治理、淘汰 | △ 層級或 agentic search | ✔ | 任務結果／因果估計 |
| SkillDAG | ✘ 文字 | △ 以邊型標示重複 | ✔ 型別圖＋agent 介面 | ✔ | 執行佐證邊＋圖的結構檢查 |
| Dual-Layer / MemForest / LAM | ✘ 文字 | ✔ 合併／壓縮 | △ | ✔（記憶由 LLM 產生） | 經驗指標；LAM 有分數界 |
| **ADVENT** | ✔ Prolog predicate | ✔ fingerprint 去重（R2） | ✔ 包含／依賴邊＋agent 工具 | ✔ | RC 統計閘門＋Prolog 演繹＋獨立驗證 |

---

## 4. 限制與需要小心的地方

1. **「保語意」的強度**：你的 R2 是在**固定參考手牌集**上算 fingerprint，語意相同＝該集合上行為相同；你另做了 5,061 手牌的獨立驗證（70 個多成員類中 14 個分裂，全是 timeout，0 個是真語意差異）。這是**經驗等價**，比 Knorf 的邏輯等價弱。建議措辭：「在參考集上行為等價（獨立驗證）」。包含邊（⊂）同理。
2. **C3 的新穎性**不在「agent 查詢結構」（SkillDAG 已有），而在結構來自可執行語意、以及 `test_rule`／`deduce` 的可執行檢查。
3. **數字的來源**：除 SkillDAG 與 Berlot-Attwell 兩組讀過內文，其餘 2026 論文的數字皆取自摘要；多數為尚未同儕審查的 preprint。引用前請對照原文。
4. **檢索覆蓋有限**：只用 arXiv API 關鍵字；未系統性檢索 Popper、Metagol、ILASP 等 ILP 主線近作；未收錄 ILP 會議（ICLP、ILP、IJCAI 等）論文集。
5. **領域差距**：skill／記憶論文的 benchmark 與你的 KANDY／Poker 不同，只能比較設計，不能比較數字。

---

## 5. 建議的行動

**實驗（優先序）**
1. **重用率統計**（零新增推理成本）：KANDY 每題解答呼叫了多少 KP predicate、被幾個不同任務重用。
2. **成本對齊 baseline**：noKP 給同樣 token／呼叫預算，再比「更省或更準」。
3. **LLM 整理對照組**：讓 LLM 對同一份原始 KP 去重歸併，對比 R0–R3（直接檢驗主論點）。
4. **命名消融**：opaque／有意義／LLM 重新命名（同時回應自審 W2）。
5. 借 LAM 的想法：報告整理前後的**可解率／檢索命中是否不變**。

**寫作**
- 措辭用「參考集上行為等價」；C3 的新穎性寫在「結構來源與可執行檢查」；相關工作寫「我們檢索到的文獻中」，不寫「首個」。
- 補讀：SkillDAG §3（邊的建立與提交協議）、Scalable Knorf 全文、Popper／Metagol。

**簡報**：v3 已新增 p.6–8（定位表、SkillDAG 對照、實驗啟示），可帶去給老師看。
