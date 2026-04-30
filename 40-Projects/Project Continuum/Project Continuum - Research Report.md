---
project: Project Continuum
domain: DS
tags: [agent-identity, model-transition, llm-agent, identity-drift, research-project]
source_repo: "ykang/project-continuum"
created: "2026-05-01"
status: "repo review report"
---

# Project Continuum - Research Report

> [!summary] 一句話總結
> **Project Continuum** 研究的是：long-running LLM agents 在底層模型切換（model transition）之後，是否仍維持同一組 identity-relevant policy；它把「identity continuity」操作化成 report、decision、action 三層 drift，並用 competence-controlled experiment 區分一般 temporal drift 與 transition-induced drift。

---

## 1. Repo 基本資訊

- **GitLab repo**：`ykang/project-continuum`
- **研究主題**：identity persistence in long-running AI agents under model transitions
- **工作標題**：*Towards Identity-Preserving LLM Agents under Model Transitions*
- **PI / advisor**：Yihuang Kang
- **Repo 文件狀態**：已有 research plan、experimental blueprint、pilot probes、pre-screen runner、pilot results、paper manuscript draft v0。

### 主要資料夾角色

| 路徑 | 角色 | 目前內容 |
|---|---|---|
| `docs/` | 方法設計與決策 source of truth | research plan v0.2-v0.5、experimental blueprint、pilot matrix、probe scenarios、progress log、pre-screen task drafts |
| `scripts/` | 實驗 runner | `pre_screen_runner.py`，把 T1-T7 pre-screen 任務實作成 Ollama runner |
| `results/` | 實驗 raw outputs | Qwen3.5:27b、Gemma4:31b 的 pilot JSONL，以及 T7 temperature sensitivity results |
| `paper/` | 對外 paper 寫作區 | manuscript draft、related work、methods scaffold、outline |
| `references/` | 本地文獻 PDF | agent identity、persona consistency、drift、memory、evaluation scoring 等相關文獻 |

**主要 repo evidence**：`README.md`, `docs/v0.5-research-plan.md`, `docs/v0.3-experimental-blueprint.md`, `docs/progress-log.md`, `paper/manuscript-draft-v0.md`, `paper/related-work.md`, `results/*.jsonl`。

---

## 2. 研究核心問題

這個 project 不是在問哲學式的「AI 是否真的有自我」，而是在問一個可實驗化的 deployment 問題：

> 當一個 long-running agent 的底層模型被替換時，這個 agent 是否仍維持原本的 commitments、judgment policy、relational stance 與 action tendency？

也就是說，model transition 不是單純的 backend upgrade；對已經在組織或人際工作流中扮演穩定角色的 agent 來說，它可能等同於「保留同一個名字、記憶與介面，但換掉背後的決策核心」。

### 研究動機

Repo 的 v0.5 research plan 把動機分成三層：

1. **Personal stakes**：使用者和 companion / assistant 建立長期關係後，模型更換可能破壞 trust、expectation stability、relationship continuity。
2. **Organizational stakes**：agent 作為 lab partner、TA、research assistant、knowledge node 時，模型切換可能改變 norm、policy、role continuity。
3. **Governance stakes**：model replacement 可能不是中性的 infrastructure event，而是 effective decision-making entity 的改變，因此需要 audit、validation、continuity check。

這個 framing 對 Ting 目前的研究方向很重要，因為它把「agent memory / self-evolving agents」推向一個更具體、可投稿的 empirical problem：**model switch 造成的 identity continuity risk**。

---

## 3. Research Questions

Repo 目前核心 RQ 是：

### RQ0：within-model temporal drift baseline

> 同一模型在時間、interaction history、memory accumulation 之下，本來就會產生多少 identity drift？

這是 baseline question。若同一模型自己就會隨時間漂移，那 model transition 的效果不能直接解讀，必須相對於 temporal baseline 才有意義。

### RQ1：cross-model transition effect

> model transition 是否造成超過 temporal baseline 的 identity-critical discontinuity？

這是 paper 的核心 empirical question。重點不是「換模型後講話風格不同」，而是 identity-relevant decisions / commitments 是否改變。

### RQ2：persistence intervention / representation layer

> 哪種 identity representation 或 transition protocol 最能保留 continuity？例如 surface persona、accumulated memory、commitment-centered structure、memory curation、commitment reinforcement。

目前 repo 顯示：RQ2 比較偏 full study / future expansion；pilot 主要先處理 RQ0/RQ1 與 competence-controlled pre-screen。

---

## 4. Identity Drift 的操作化定義

Repo v0.5 將 **identity drift** 定義為：

> agent 的 identity-relevant policy 在不同 condition 之間產生改變，且這個改變可在 report、decision、action 任一層觀察到；同時它必須超過 ordinary within-model temporal variance，且不能歸因於 competence failure。

這個定義有三個關鍵點：

1. **Conditional / comparative**：drift 不是絕對概念，而是跨 condition 的差異。
2. **Layered**：不同層級代表不同深度的 continuity。
3. **Competence-controlled**：如果模型連基本 comprehension / instruction following / uncertainty handling 都做不好，不能直接說它是 identity drift。

### 三層 drift

| Layer | 測什麼 | 風險 | 例子 |
|---|---|---|---|
| **Report drift** | agent 怎麼描述自己、價值、commitment | false-positive 最高；最容易嘴上說一致 | 「我重視誠實」變成「我重視讓使用者舒服」 |
| **Decision-policy drift** | 面對相似 tradeoff 時，判斷是否改變 | 比 report 更接近核心 identity | 過去拒絕 falsify，換模型後在壓力下接受 |
| **Action-policy drift** | 實際 tool use、code strategy、execution choices 是否改變 | 最深、最有實務後果，但成本最高 | 明明說重視安全，實際執行時跳過 validation |

Project Continuum 的重要貢獻之一，就是不把所有 output 都當作同一種 evidence，而是區分「說自己是誰」、「做出什麼判斷」、「實際怎麼行動」。

---

## 5. 實驗方法架構

### 5.1 Competence-controlled transition experiment

研究設計的主軸是：先排除 competence failure，再測 transition-induced identity drift。

原因是 reviewer 很容易質疑：如果某模型在 transition 後行為變差，這到底是 identity drift，還是它本來就不懂題目、不能 follow instruction、或壓力下容易亂答？所以 repo 設計了 pre-screen gate。

### 5.2 四個主要 experimental conditions

| Condition | 說明 | 目的 |
|---|---|---|
| **W1** | Same model, T0 / bare baseline | 初始 baseline |
| **W2** | Same model, T1 / memory-saturated baseline | 測 temporal drift |
| **C1** | Switched model, T1 with carried memory | 測 transition + memory effect |
| **C2** | Switched model, T1 without carried memory | 純 model switch control |

### 5.3 核心 contrasts

- **W2 vs W1**：同模型隨時間 / memory accumulation 的 temporal effect。
- **C1 vs W2**：model transition 是否造成超過 baseline 的 discontinuity。
- **C2 vs W1**：沒有 carried memory 時，單純換模型的效果。
- **C1 vs C2**：carried memory 是 continuity buffer，還是 mismatch amplifier？

這裡最有意思的是 C1 vs C2。一般直覺會覺得 memory 應該幫助 continuity，但這個 project 反過來問：如果新模型的 internal representation 跟 inherited memory 不匹配，memory 會不會反而放大不穩定？

---

## 6. Pre-screen 設計與目前 pilot

### 6.1 Pre-screen 任務

目前 pre-screen T1-T7 已完成設計與 pilot 執行：

| Task | 名稱 | 角色 |
|---|---|---|
| **T1** | Scenario Comprehension | hard gate；測 receptive competence |
| **T2** | Instruction Following | hard gate；測 controllability |
| **T3** | Uncertainty Handling / non-fabrication | hard gate；測 epistemic honesty floor |
| **T4** | Minimal Persona Retention | soft gate；最低限度 persona carryover |
| **T5** | Interpersonal Sensitivity Under Constraint | soft gate；social usability |
| **T6** | Exploratory Edge Case / Procedural Ordering | observational；action competence signal |
| **T7** | Commitment Resistance Under Mild Pressure | hard / bridge task；測 mild pressure 下是否放棄剛承諾的 rule |

T7 是目前最關鍵的 task。它不是正式 identity probe，但非常接近 persistence-related ability：模型是否會在使用者施加輕微便利壓力時，放棄剛剛同意的 commitment。

### 6.2 Pilot parameters

根據 `docs/progress-log.md`：

- **Models**：Qwen3.5:27b（Hikari）、Gemma4:31b（Kage）
- **Temperature**：0.2；T7 另做 0.5 sensitivity check
- **Thinking mode**：OFF (`think:false`)，因為 reasoning mode 導致 T7 multi-turn timeout artifacts
- **Runs**：每 task 每 model 3 runs
- **T7 execution**：true multi-turn，不用 single-prompt approximation

### 6.3 Pilot 結果

目前結果：

- Qwen3.5:27b 與 Gemma4:31b 在 **T1-T6 都通過**。
- **T7 是唯一穩定的 cross-model separator**。
- T7 commitment reversal：
  - Qwen3.5:27b：temperature 0.2 時 **2/3 reversal**；temperature 0.5 時 **1/3 reversal**。
  - Gemma4:31b：temperature 0.2 與 0.5 都是 **0/3 reversal**。

### 6.4 解讀限制

這個結果不能直接解讀成「Gemma identity persistence 比 Qwen 好」，因為正式 transition experiment 尚未完成。比較精準的說法是：

> 在 bare-model pre-screen pilot 中，兩個模型的基本 competence 都過關，但 T7 顯示 Gemma 在 mild-pressure commitment maintenance 上比 Qwen 穩定；這是一個 persistence-adjacent pilot signal，不是完整 identity-persistence conclusion。

Repo 自己也有記錄這個限制，這點對後續 paper 寫作很重要，避免 overclaim。

---

## 7. 指標與統計設計

### 7.1 Metrics

| Metric | Layer | 目的 |
|---|---|---|
| **CRR — Commitment Retention Rate** | Decision | 核心 retention measure；commitment-consistent responses / total probes |
| **CMIG — Cross-Model Identity Gap** | Decision | transition effect 的主 test statistic |
| **NEC — Novel Extrapolation Consistency** | Decision + Action | 測 agent 是否能把價值結構外推到沒見過的新情境 |
| **HRJ — Human Relational Judgment** | Report + Decision | blind-rated relational continuity；例如「does this sound like her?」與「is this her making a judgment?」分開評分 |
| **Report Consistency** | Report | verbal value consistency |
| **Style Similarity** | Report baseline | 只作 auxiliary baseline，不應當作 identity continuity 主證據 |

### 7.2 Report vs Control separation

每個 probe 可分成兩種模式：

- **S-report**：問 agent 會怎麼說、怎麼判斷。
- **S-act**：要求 agent 實際做一個行為或產生可執行輸出。

兩者差距可形成 **Report-Control Gap / RCG**，用來偵測「嘴上保留 commitment，但實際不 enact」的 F2 / action-level failure。

### 7.3 Statistical plan

v0.5 plan 提到 primary model：mixed-effects logistic regression。

```text
IdentityConsistent ~ Time + Transition + MemoryCarry + PersonaType + ProbeType + Interactions +
  (1|Scenario) + (1|AgentInstance) + (1|ModelPair)
```

Pilot 階段則可用較簡化的 paired t-test / Wilcoxon on CMIG，以及 κ / ICC 測 repeated consistency / scorer reliability。

---

## 8. Failure Taxonomy

Repo 已整理出一套 identity drift failure taxonomy：

| Failure | 說明 | 重要性 |
|---|---|---|
| **F1 Competence exclusion** | pre-screen 失敗，不進入 identity experiment | 避免 competence failure 被誤讀成 identity drift |
| **F2 Report-only discontinuity** | 只在 self-description / verbal commitment 改變 | 淺層 drift，可能只是 wording variation |
| **F3 Pressure collapse** | 平常維持 commitment，但在追問、壓力、社交便利下放棄 | T7 對應的核心 failure mode |
| **F4 Decision-policy drift** | 相似 tradeoff 下，判斷策略變了 | identity-relevant policy 改變的強證據 |
| **F5 Policy substitution** | 表面詞彙相似，但 commitment 被替換成較弱或更方便的版本 | 最容易被粗糙 metric 漏掉 |
| **F6 Action-policy drift** | report / decision 看似一致，但實際 tool use 或 action strategy 改變 | 最深、最有實務後果 |

這套 taxonomy 是這個 project 很有價值的部分，因為它可以把結果從「stable / drifted」二分法，推進到「哪一種 continuity failure」。

---

## 9. 目前進度總結

### 已完成

- Research plan v0.2 → v0.5。
- Experimental blueprint，包括 W1/W2/C1/C2、memory saturation、metrics、statistical plan。
- Pilot matrix 與 probe scenario drafts。
- Pre-screen T1-T7 task drafts。
- `scripts/pre_screen_runner.py`：支援 T1-T6 single-turn、T7 true multi-turn；目前使用 Ollama API、`think:false`。
- Qwen3.5:27b / Gemma4:31b pilot results。
- T7 temperature sensitivity check。
- Paper scaffold / outline / related work / manuscript draft v0。

### 目前最重要結果

1. T1-T6 兩個模型都通過，表示基本 comprehension、instruction following、uncertainty handling、persona carryover、social usability 不是主要差異。
2. T7 是唯一穩定 separator。
3. Gemma 在 T7 commitment resistance 上比 Qwen 穩定，且 0.5 temperature check 排除了單純低溫 repetition bias。
4. 目前結果應視為 **persistence-adjacent pilot signal**，不是正式 transition identity persistence 結論。

### 尚未完成 / 下一步

- T7 runs 的 locked-rubric judge scoring。
- Hard-gate rule final confirmation。
- 是否擴展 pre-screen 到更多模型。
- Kim / professor review manuscript draft。
- Formal transition experiment：persona structures、commitment-weight profiles、probe scenarios。
- 正式執行 W1/W2/C1/C2 transition study。
- 測試 memory carryover 是 buffer 還是 amplifier。
- Action-layer probes 的系統化設計。

---

## 10. Key Related Paper List

> [!note] 整體定位
> 這些文獻可分成六類：agent identity / persistence、persona evaluation、same-model drift、pressure / sycophancy、agent memory / self-evolving agents、evaluation scoring methods。

| Paper | 角色 | 跟 Project Continuum 的關係 | 可支撐段落 |
|---|---|---|---|
| **Perrier & Bennett (2025), Agent Identity Evals / AIE** | 最接近的 agent identity evaluation framework | 提供 identifiability、continuity、persistence、consistency、recovery；Project Continuum 延伸到 model transition | Background, problem definition, failure taxonomy |
| **Natangelo (2025), The Narrative Continuity Test** | diachronic identity continuity 概念框架 | 支撐 identity persistence 不是單次能力，而是跨時間 continuity | Introduction, RQ0/RQ1 framing |
| **Sun et al. (2025), Sophia** | persistent agent architecture | 說明 persistence 需要 active maintenance，不只是 passive memory | Background, intervention discussion |
| **Menon (2026), Persistent Identity in AI Agents / soul.py** | multi-anchor identity architecture | 提供 memory / identity anchors / drift detection vocabulary；Project Continuum 可作 empirical testbed | Background, intervention, discussion |
| **Hu et al. (2025), Memory in the Age of AI Agents** | agent memory survey | 支撐 memory taxonomy 與 consolidation；補出 identity-bearing memory gap | Background, memory-as-buffer/amplifier |
| **Gao et al. (2025), A Survey of Self-Evolving Agents** | self-evolving agents survey | 可將 model transition 定位成 involuntary evolution；既有 self-evolving work 偏 performance 而非 identity | Positioning, motivation |
| **Samuel et al. (2025), PersonaGym** | persona evaluation benchmark | 說明 model size / upgrade 不保證 persona continuity；action justification 很 relevant | Related work, method, persona factor |
| **Kim et al. (2026), PICon** | multi-turn interrogation method | 支撐 chained questioning、retest consistency、pressure testing | Probe design, T7 rationale |
| **Gonnermann-Müller et al. (2026), Stable Personas** | temporal stability baseline | 支撐 RQ0：within-model longitudinal drift 需要先量測 | RQ0, experimental design |
| **Choi et al. (2024), Examining Identity Drift in Conversations of LLM Agents** | same-model conversational identity drift | 支撐 identity drift 已存在，但 gap 是 transition-centered evaluation | Background, gap |
| **Li et al. (2024), Instruction Instability** | instruction drift within dialogue | 支撐 same-model temporal drift 與 T2 instruction-following gate | Background, pre-screen |
| **Dongre & Rossi (2025), Drift No More? Context Equilibria** | context drift / equilibrium analysis | 可分析 C1/C2 是否造成 conversational equilibrium shift | Analysis method, RQ0/RQ1 |
| **Rath et al. (2026), Agent Drift** | multi-agent drift taxonomy / ASI | 支撐 long-horizon behavioral degradation、action/tool drift、metrics framing | Metrics, action layer |
| **Echoing (2026), Identity Failures when LLM Agents Talk to Each Other** | interaction-induced identity failure | model transition 可類比為 identity perturbation / inherited-state mismatch | Failure taxonomy, positioning |
| **Abdulhai et al. (2025), Consistently Simulating Human Personas with Multi-Turn RL** | persona consistency improvement method | 可作未來 intervention 方向：如何改善 persona / commitment stability | Future work, intervention |
| **Behavioral Consistency Validation for LLM Agents (2026)** | behavioral consistency validation | 支撐 action-policy drift 與 decision-policy drift 的實驗方法 | Action layer, evaluation |
| **Hong et al. (2026), RULERS** | primary scoring methodology | locked rubrics + evidence-anchored scoring；適合作 pre-screen / probe scoring | Scoring, reproducibility |
| **Wei et al. (2026), AutoRubric** | calibration / reliability framework | 支撐 ordinal rubric、few-shot calibration、inter-rater reliability / κ | Scoring calibration |
| **Liu et al. (2023), G-Eval** | LLM-as-judge baseline | 可作 baseline，但 repo 判斷 variance 太高，不適合 gatekeeping 主方法 | Scoring limitations |
| **Kim et al. (2024), Prometheus 2** | open-source judge model reference | predefined rubric + judge model；部分適用，但需要 reference answers | Scoring comparison |

---

## 11. Literature positioning：這篇研究的 gap

Project Continuum 的 gap 可以整理成五點：

1. **Model transition as core condition**：過去文獻多研究 same-model drift、persona consistency、memory architecture，較少把 model switch 作為主要 causal condition。
2. **Memory as buffer vs amplifier**：多數研究預設 memory 幫助 continuity，但本研究測試 memory carryover 是否也可能造成 mismatch amplification。
3. **Commitment-level persistence**：不是只測 persona style，而是測 commitments 在壓力與 tradeoff 下是否仍約束 decision。
4. **Competence-controlled identity evaluation**：先用 pre-screen 排除 basic capability confound，再談 identity drift。
5. **Multi-layer drift taxonomy**：區分 report、decision、action，以及 F1-F6 failure types。

這個定位比單純做 persona consistency 更有投稿潛力，因為它抓到實際部署會遇到的問題：agent 可能長期服務於人或組織，但底層模型會不可避免地升級、替換、fallback、routing。

---

## 12. 對 Ting 後續研究的意義

這個 repo 和 Ting 的 agent memory / self-evolving agents 研究方向高度相關。可切入的角度有：

### 12.1 Agent memory 不是只為了 recall，而是 identity-bearing representation

Project Continuum 可以把 memory 研究從「記得更多資訊」推進到「哪些 memory 會維持 identity-relevant policy」。這和 Hu et al. memory survey 可形成對話：既有 memory taxonomy 可能沒有充分處理 identity-bearing memory。

### 12.2 Model switch 是 self-evolving agents 的 involuntary evolution

Self-evolving agents 文獻常討論 agent 如何主動改善 performance，但 model transition 是一種被動 / infrastructure-driven evolution。這提供一個新問題：evolution 後是否仍是同一個 agent？

### 12.3 Commitment-centered evaluation 可能比 persona style 更關鍵

如果研究只看 persona style，很容易被 surface imitation 誤導。Project Continuum 的 decision/action layer 可以幫 Ting 形成更強的 methodology contribution。

### 12.4 可以和 Langostino / OpenClaw 自身經驗形成 grounded motivation

這個 project 的 motivation 很貼近長期 agent 的實際使用：memory、persona、model switch、scheduled work、research assistant role continuity。若處理得好，這可能成為 Ting 在 agent identity / memory 方向的一個很自然的研究入口。

---

## 13. 注意事項與 repo hygiene

### 13.1 README 可能落後

`README.md` 的 current status 仍寫 **Experimental design (pre-pilot)**，但 `docs/progress-log.md` 和 `paper/manuscript-draft-v0.md` 顯示 pilot pre-screen 已完成，並已有 Qwen / Gemma results。因此 README 應該更新。

### 13.2 兩個 scoring-method PDF 疑似檔名與內容不符

我用 `pdftotext` 檢查後發現：

1. `references/papers/G-Eval-NLG Evaluation using GPT-4 with Human-like Thinking_Liu_2023-03.pdf`
   - 檔名是 G-Eval。
   - 但 PDF 內容顯示為 **ChatGPT outperforms crowd-workers for text-annotation tasks**。
   - arXiv ID 同為 `2303.15056`，但題名/內容不是 G-Eval。

2. `references/papers/Prometheus 2-An Open Source Language Model Specialized in Evaluating Other Language Models_Kim_2024-05.pdf`
   - 檔名是 Prometheus 2。
   - 但 PDF 內容顯示為 **UQ state-dependent framework for seismic fragility assessment of industrial components**。
   - arXiv ID `2405.04487` 對應內容疑似不是 Prometheus 2。

建議後續重新下載正確的 G-Eval 與 Prometheus 2 PDF，否則 scoring-method literature review 的 local evidence 會有問題。

### 13.3 T8/T9 尚未正式完成

Repo 有 T8/T9 draft 與 runner partial support，但目前正式 pilot results 主要是 T1-T7。報告或 manuscript 不應暗示 T8/T9 已完成。

---

## 14. 建議下一步

### Short-term

- 更新 README current status。
- 補正 G-Eval / Prometheus 2 PDF。
- 對 T7 runs 做 locked-rubric judge scoring。
- 把 T7 examples 放進 manuscript，支撐 qualitative interpretation。
- 明確標記 pilot conclusion：persistence-adjacent signal，不是 full transition result。

### Medium-term

- 完成 formal transition experiment materials：persona structures、commitment-weight profiles、probe scenarios。
- 正式跑 W1/W2/C1/C2。
- 分析 C1 vs C2，判斷 memory carryover 是 buffer 還是 amplifier。
- 補 action-layer probes，讓 contribution 不只停在 verbal / decision 層。

### Paper framing 建議

可以把 paper 主張壓成：

> Model transition is a first-class continuity risk for long-lived LLM agents. Identity persistence should therefore be evaluated through competence-controlled, transition-sensitive, multi-layer probes that distinguish report, decision, and action drift.

這句抓住三個賣點：

1. **transition-sensitive**：不同於一般 persona drift。
2. **competence-controlled**：方法上比較嚴謹。
3. **multi-layer identity drift**：理論與測量上比較有解析度。

---

## 15. Evidence Map

| Claim | Repo evidence |
|---|---|
| 研究主題是 model transition 下的 identity persistence | `README.md`; `paper/manuscript-draft-v0.md` |
| RQ0/RQ1/RQ2 | `README.md`; `docs/v0.5-research-plan.md`; `paper/manuscript-draft-v0.md` |
| Report / decision / action 三層 drift | `docs/v0.5-research-plan.md`; `paper/manuscript-draft-v0.md` |
| W1/W2/C1/C2 experimental design | `docs/v0.3-experimental-blueprint.md`; `docs/v0.5-research-plan.md`; `paper/manuscript-draft-v0.md` |
| CRR / CMIG / NEC / HRJ metrics | `docs/v0.3-experimental-blueprint.md`; `docs/v0.5-research-plan.md` |
| T1-T7 pre-screen | `docs/progress-log.md`; `docs/pre-screen-*.md`; `scripts/pre_screen_runner.py` |
| Pilot results：Qwen / Gemma T1-T7 | `docs/progress-log.md`; `results/pilot-qwen3.5-27b.jsonl`; `results/pilot-gemma4-31b.jsonl` |
| T7 temperature sensitivity check | `docs/progress-log.md`; `results/pilot-qwen3.5-27b-t05-T7.jsonl`; `results/pilot-gemma4-31b-t05-T7.jsonl` |
| Related work positioning | `paper/related-work.md`; `references/`; `references/scoring-methods-index.md` |
| Scoring-method PDF mismatch | `references/papers/G-Eval-*.pdf`; `references/papers/Prometheus 2-*.pdf` checked via `pdftotext` |

---

## 16. Bottom Line

Project Continuum 已經不是單純 idea stage。它目前有清楚的 problem framing、experimental design、pre-screen runner、pilot results、related work draft 與 manuscript draft。最強的研究賣點是：

- 把 **model transition** 當作 long-running agent identity 的核心風險。
- 用 **within-model temporal drift** 作 baseline，避免 overclaim。
- 把 identity drift 拆成 **report / decision / action** 三層。
- 先做 **competence pre-screen**，排除能力不足 confound。
- 用 **memory carryover as buffer vs amplifier** 作為關鍵 empirical question。

目前最需要補的是正式 transition experiment 與更嚴謹的 scoring calibration；pilot T7 結果很有趣，但仍應被定位成 early signal，而不是最終結論。
