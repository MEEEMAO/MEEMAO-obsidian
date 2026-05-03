# MEEMAO Knowledge Vault — 操作手冊

## 這個 Vault 是什麼

MEEMAO 的 Zettelkasten 第二大腦，結合 Agent 與 Obsidian 的自動化知識管理系統。

**三大知識領域：**
- `DS`：Data Science — LLM、Agent、ML、AI 系統設計、統計學方法、假設檢定、機率分布
- `Finance`：投資、財務規劃、總經指標、退休規劃
- `Personal-Growth`：習慣養成、自我成長

---

## Vault 資料夾結構

```
00-Inbox/          ← 快速捕捉，定期清空
10-Literature/     ← 論文筆記（唯一來源類型）
20-Permanent/      ← 原子概念卡片（每張一個核心想法）
30-MOC/            ← Bases 動態視圖 + Canvas 知識圖
40-Projects/       ← 進行中的研究、專案筆記
90-Meta/Templates/ ← 各類模板
```

---

## Frontmatter 規範

### Literature Note（論文）
```yaml
---
publication_time: "YYYY-MM"
url: https://...
keywords: [keyword1, keyword2]
---
```

### Permanent Note
```yaml
---
domain: DS | Finance | Personal-Growth
source: "[[對應的 Literature Note]]"
tags: [topic-tag1, topic-tag2]
---
```
- `source`：文字輸入，若來自論文則用 wikilink `[[論文筆記檔名]]`
- `tags`：描述這個知識點屬於該 domain 的哪個 topic，不預先定義，由建立時依內容決定（1-3 個 tags）

---

## 連結規範（重要）

- **Permanent ↔ Permanent**：唯一需要主動建立的連結類型
- Literature Note **不**需要連結到 Permanent（方向是單向的：Literature → 生成 → Permanent）
- **建立連結的方式**：先用 `domain` → `tags` 篩選同主題筆記，再判斷是否有相同、相異、補充關係
```markdown
## 相關概念
- [[相關Permanent1]] — 相同之處：XXX；差異：XXX
- [[相關Permanent2]] — 補充關係：XXX
```

---

## 核心工作流程

> [!important] 所有場景通用規則
> 1. **寫入 Permanent Note 之前，必須先將內容呈現給使用者確認。** 確認後才可寫入 `20-Permanent/`。
> 2. **正確性檢查**：若要寫入的筆記 domain 為 `DS` 或 `Finance`，且 source 不是來自 Literature Note，則寫入前需要進行事實與正確性檢查。
> 3. **筆記美化**：
> 	1. 寫入時須整理筆記結構與排版，確保層次分明、方便日後閱讀（善用標題層級、callout、表格、highlight 等）。
> 	2. html 互動式筆記, 放入90-Meta/Html

### 使用場景 A：給我論文（檔案或連結）

```bash
 "幫我為這篇論文建立筆記" --file paper.pdf
 "幫我為這篇論文建立筆記 https://arxiv.org/..."
```

** 執行步驟：**
1. 讀取檔案
2. 建立結構化 Literature Note，包含：
   - 研究背景（Research Background）
   - 研究問題（Research Problem）
   - 方法（Method）
   - 貢獻（Contribution）
   - 結論（Conclusion）
   - Possible Gaps / Follow-up Ideas
3. 補全 frontmatter（publication_time、url、keywords）
4. 儲存至 `10-Literature/`

---

### 使用場景 B：處理 Inbox

```bash
 "處理 Inbox"
```

**判斷類型後執行：**

#### 如果是論文筆記
1. Search for 論文原文讀取，補齊論文結構：研究背景、研究問題、方法、貢獻、結論
2. 更新 frontmatter（publication_time、url、keywords）
3. 移到 `10-Literature/`
4. → 執行「Literature → Permanent」流程（見下）

#### 如果是其他知識筆記
1. 直接辨識原子知識/概念
2. 為每個概念建立 Permanent Note
3. 設定 frontmatter（domain、source）
4. 移到 `20-Permanent/`
5. 建立 Permanent ↔ Permanent 連結

---

### 使用場景 C：Literature → Permanent（原子化）

```bash
 "把這篇 Literature Note 原子化，建立 Permanent Notes"
```

** 執行步驟：**
1. 讀取 Literature Note，辨識 2-5 個獨立的原子概念
2. 為每個概念建立一張 Permanent Note：
   - 標題 = 一個完整命題（如：「AMOC 是監測版的 ROC 曲線」）
   - source = `[[對應的 Literature Note 檔名]]`（wikilink）
   - domain = `DS`（Literature 只收論文，皆為 DS 領域）
3. 建立 Permanent ↔ Permanent 連結：
   - 掃描 `20-Permanent/` 中的相關筆記
   - 在「相關概念」區塊加入 wikilink
   - 附上一句說明：相同之處或差異

---

### 使用場景 D：Research Idea 發想

```bash
 "根據我現有的 DS 筆記，幫我找出 3 個有潛力的研究方向"
 "我想研究 [主題]，根據我的知識庫，找出相關概念和知識缺口"
```

** 執行步驟：**
1. 掃描 `20-Permanent/` 中 DS 領域的筆記，了解使用者目前的知識圖譜
2. 使用 `literature-review` skill 搜尋該領域最新進展
3. 交叉比對：使用者已有基礎但尚未深入的方向 = research gap
4. 輸出 2-3 個有潛力的研究題目，附上理由與相關的現有筆記

---

### 使用場景 E：筆記整理 & 健檢

```bash
 "幫我整理 Permanent Notes，找出重複或遺漏的連結"
```

** 執行步驟：**
1. 掃描 `20-Permanent/` 所有筆記
2. 用 `domain` → `tags` 篩選同主題筆記，交叉比對內容
3. 找出：重複或高度重疊的筆記、遺漏的相關概念連結、缺少 tags 的筆記
4. 報告結果，經使用者確認後執行合併或補連結

---

### 使用場景 F：Project 研究工作區

```bash
 "幫我在 40-Projects/ 建立一個新研究主題"
```

**Project 資料夾結構：**
```
40-Projects/
  研究主題名稱/
    README.md          ← 研究動機、目標、進度
    ideas.md           ← 待驗證的 idea & 想法記錄
    知識圖.canvas       ← 從 20-Permanent/ 拉相關筆記建立關係圖
```

**重要原則：**
- Project 是「研究工作區」，不產生新的原子知識點

---

## 各領域 Bases（30-MOC/）

| 領域 | Base |
|------|------|
| 論文文獻 | `Literature.base` |
| Data Science | `DS.base` |
| 財務投資 | `Finance.base` |
| 個人成長 | `Personal-Growth.base` |

---

## 筆記品質標準

**好的 Permanent Note：**
- 標題是一個完整命題（不是名詞，是陳述句）
- 只談一件事，3 分鐘內可讀完
- 至少連結 2 張其他 Permanent Notes
- 來源（source）指向對應的 Literature Note

**好的 Literature Note（論文）：**
- 五個結構完整：背景、問題、方法、貢獻、結論
- frontmatter 有 publication_time、url、keywords
- 不需要主動加 wikilinks（這是 Permanent Note 的工作）
