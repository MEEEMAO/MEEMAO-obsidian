# MEEMAO Knowledge Vault — 操作手冊

## 這個 Vault 是什麼

MEEMAO 的 Zettelkasten 第二大腦，結合 Agent 與 Obsidian 的自動化知識管理系統。

**知識領域：**
- `DS`：Data Science — LLM、Agent、ML、AI 系統設計、統計學方法、假設檢定、機率分布
- `Math`: 線性代數、離散數學
- `Finance`：投資、財務規劃、總經指標、退休規劃

---

## Vault 資料夾結構

```
00-Inbox/          ← 快速捕捉，定期清空
10-Literature/     ← 論文筆記（唯一來源類型）
20-Permanent/      ← 原子概念卡片（每張一個核心想法）
30-MOC/            ← Bases 動態視圖 + Canvas 知識圖
40-Projects/       ← 進行中的研究、專案筆記
90-Meta/Templates/ ← 各類模板
90-Meta/images/ ← 各類筆記解說圖
90-Meta/Html/ ← 各類筆記解說視覺講義
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
source: "[[]]"
tags: [topic-tag1, topic-tag2]
---
```
- `source`：文字輸入，若來自論文則用 wikilink `[[論文筆記檔名]]`
- `tags`：描述這個知識點屬於該 domain 的哪個 topic（1-3 個 tags）

### Inbox Note
```yaml
---
domain: DS | Finance | Personal-Growth
source: 
tags: [topic-tag1, topic-tag2]
---
```
- `tags`：描述這個知識點屬於該 domain 的哪個 topic（1-3 個 tags）

---

## 連結規範（重要）

- 需要主動建立的連結類型: **Permanent ↔ Permanent** 
	- 先用 `domain` → `tags` 篩選同主題筆記，再判斷是否有相同、相異、補充關係
- - Literature and Permanent 不需要 link, 直接將 wikilink 設為Permanent source
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

---

### 使用場景：處理 Inbox

```bash
 "處理 Inbox"
```

1. 辨識原子知識/概念
2. 為每個概念建立 Permanent Note
3. 設定 frontmatter（domain、source）
4. 移到 `20-Permanent/`
5. 檢查是否需要建立 Permanent ↔ Permanent 連結

---

### 使用場景 ：Literature → Permanent（原子化）

```bash
 "把這篇 Literature Note 原子化，建立 Permanent Notes"
```

** 執行步驟：**
1. 讀取 Literature Note，辨識 2-5 個獨立的原子概念
2. 為每個概念建立一張 Permanent Note：
   - 標題 = 一個完整命題
   - source = `[[對應的 Literature Note 檔名]]`（wikilink）
   - domain = `DS`（Literature 只收論文，皆為 DS 領域）
3. 掃描 `20-Permanent/` 中的相關筆記, 檢查是否要建立 Permanent ↔ Permanent 連結
