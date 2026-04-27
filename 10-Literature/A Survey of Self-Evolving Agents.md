---
publication_time: 2025-07
url: https://arxiv.org/abs/2507.21046
keywords:
  - self-evolving-agents
  - LLM-agents
  - agent-evolution
---
## Research Background

大型語言模型（LLM）本質上是**靜態**的——一旦訓練完成，模型參數便無法根據新任務或動態環境自適應地調整。這在需要持續學習和適應的真實世界部署場景中形成了根本性瓶頸。受達爾文「最能適應環境變化的物種才能存活」的啟發，研究社群開始將焦點從「擴大靜態模型規模」轉向「發展能自我演化的智慧體（Self-Evolving Agents）」，視其為通往人工超級智慧（ASI）的關鍵路徑。

> [!info] Self-Evolving Agent operational definition:
> The agent that modifies its internal parameters, contextual state, toolset, or architectural topology based on its own trajectories or feedback signals, with the explicit objective of improving future performance.
> 
> **Three inclusion criteria:**
> 1. Updates must be **experience-dependent**, driven by trajectories or environment feedback
> 2. Updates must produce **persistent, policy-changing** effects (not transient behaviors)
> 3. System must possess **autonomous exploration** or **self-initiated learning** mechanisms

**Self-evolving agent = 多層級可進化系統**

- Agent 可以演化的**組件**有哪些？（模型、記憶、工具、架構）
- **何時**該觸發演化？（任務執行中 vs. 任務之間）
- **如何**驅動演化？（獎勵訊號、模仿學習、群體演化）
- 在哪些**領域**中應用演化？

 frontier ：**同時在四個維度 co-evolve**

## Method
![[Pasted image 20260421135720.png]]

### What to Evolve（演化什麼）

| 組件               | 說明                                                 |
| ---------------- | -------------------------------------------------- |
| **Models**       | 策略參數更新、基於經驗的學習                                     |
| **Context**      | 記憶結構（ADD/UPDATE/DELETE）、Prompt 優化（如 SPO、DSPy）      |
| **Tools**        | 工具創建（如 Voyager 的技能庫）、工具精熟（如 DRAFT）、工具管理（如 ToolGen） |
| **Architecture** | 單一 Agent 與多 Agent 系統的拓撲演化                          |

### When to Evolve（何時演化）

- **Intra-test-time**：任務執行期間的即時適應（in-context learning、SFT、RL）
- **Inter-test-time**：不同任務之間的跨任務演化

### How to Evolve（如何演化）

- **Reward-based**：文字回饋、內部/外部獎勵、隱式訊號
- **Imitation learning**：自我生成、跨 Agent、混合式示範
- **Population-based**：單/多 Agent 系統的演化算法
- **Cross-cutting**：線上/離線學習、on/off-policy、獎勵粒度

## Contribution

1. **統一理論框架**：以 What / When / How / Where 四維度組織自我演化 Agent 的研究，為未來系統設計提供指導
2. **評估基準調查**：系統性回顧針對 self-evolving agents 的 benchmarks 與評估指標（適應性、魯棒性、真實世界複雜度）
3. **跨領域應用展示**：涵蓋自動化軟體工程、個人化教育、醫療保健、GUI 自動化、金融市場
4. **挑戰與方向辨識**：安全性、個人化、多 Agent 共演化、可擴展性

### 代表性方法

| 方向 | 代表方法 | 核心機制 |
|------|----------|----------|
| Model Evolution | Self-Challenging Agent (SCA) | Agent 自主生成任務並在成功軌跡上 fine-tune |
| Context Evolution | TextGrad | 將非結構化文字回饋視為可微分訓練訊號 |
| Context Evolution | SPO | 無需外部標籤的全自包含 prompt 優化 |
| Tool Evolution | Voyager | 在開放環境中透過試錯建立擴展技能庫 |
| Architecture Evolution | AgentGen | 合成多樣化模擬世界，支援課程式學習 |

## Conclusion

- Self-evolving agents 代表從靜態 LLM 到動態適應系統的**範式轉移**，是邁向 ASI 的關鍵步驟
- 與 curriculum learning、lifelong learning、model editing 等相關範式的區別在於：self-evolving agents 是**系統層級**的解決方案，能同時演化參數、上下文、工具與架構
- 當前完全自主的自我演化仍是**理想目標**而非現實常態，但研究正快速推進
- 主要挑戰包括：演化過程中的**安全可控性**（防止 reward hacking）、**災難性遺忘**的緩解、跨領域泛化、以及多 Agent 共演化生態系統的協調

> [!warning] 開放問題
> - 現有 benchmarks 對適應性、記憶保持和泛化能力的覆蓋不足
> - 缺乏長期（數年尺度）的 lifelong learning 評估
> - 需要標準化的公平比較協議
