---
name: literature-review
description: Conduct literature reviews and paper surveys for academic research. Use when asked to survey papers on a topic, build a related work section, find references for a paper, do a literature review, compile a reading list, or review specific papers. Covers search strategy, screening workflow, IMRaD deep-read protocol, cross-paper synthesis, method comparison, and structured output. Also use for daily paper digests and ongoing paper tracking.
---

# Literature Review & Paper Survey — Agent Skill

Systematic workflow for finding, screening, reading, comparing, and synthesizing academic papers.
Designed as a reusable skill for Claude, Codex, or any AI coding/research agent.

---

## Execution Mode

- Delegate to a subagent by default (lighter model for search/screening, stronger model for deep synthesis)
- Main session stays responsive — never block for >10 minutes
- For individual paper reviews (≤2 papers), main session can handle directly

---

## Phase 1: Scope & Seed

1. **Define scope**: target venue, page budget, research questions, specific problem context
2. **Collect seed references** from the project's existing bibliography
3. **Identify search keywords** from seed papers + research questions
4. **Set target count**: ~20-30 for conference papers, ~50-80 for journal/thesis

---

## Phase 2: Search

### Sources (priority order)
1. **Semantic Scholar API** — structured metadata, citation graph
   - Endpoint: `https://api.semanticscholar.org/graph/v1/paper/search`
   - Fields: `title,authors,year,venue,citationCount,abstract,externalIds,url`
   - Rate limit: 100 req/5min without key; add 2s delays between calls
2. **PubMed / NCBI E-Utilities** — biomedical literature
   - Search: `eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi` → Fetch: `efetch.fcgi`
   - Use MeSH terms for medical topics; avoid ambiguous abbreviations
3. **Google Scholar** (via web search) — broader coverage
4. **arXiv API** — preprints, latest ML/AI work
5. **Conference proceedings** — venue-specific browsing
6. **Backward/forward citation chasing** — from high-relevance seed papers only

### Search strategy
- Run multiple queries with different keyword combinations
- Prioritize published papers (journals, proceedings) over preprints
- Cast wide first (10-15+ candidates per query), then screen
- Track all searched queries in a log for reproducibility
- When an API is rate-limited (429), wait and retry or switch to next source

---

## Phase 3: Screening (Progressive Depth)

```
Level 1: Title + Abstract (batch, fast)
  → Keep / Maybe / Discard
  → Aim for 2-3× target count at this stage

Level 2: Introduction + Conclusion (kept papers only)
  → Extract: research problem, contribution, main finding
  → Relevance: High / Medium / Low

Level 3: Method + Results (High relevance only)
  → Full IMRaD extraction (see Phase 4)
```

**Rule: Only summarize sections you actually read.** Never fabricate method details from the abstract.

---

## Phase 4: Deep Read (IMRaD Protocol)

For papers that pass Level 3 screening:

### 4a. IMRaD Decomposition
| Section | Extract |
|---------|---------|
| **Introduction** | Problem statement, prior work cited, gap claimed |
| **Method** | Tools, pipeline, model architecture, key assumptions, hyperparameters |
| **Results** | Key figures/tables, effect sizes, statistical tests, datasets used |
| **Discussion** | Comparison with prior work, limitations acknowledged, future work |

### 4b. Cross-Paper Connections
- Can Paper A's method solve Paper B's stated limitation?
- Do different papers' results contradict each other?
- What methodological choices differ and why?
- Which papers cite each other and how?

### 4c. Relevance Rewrite
Rewrite in the user's own research framing:
- "What does this paper mean for our research?"
- "Which specific insight can we directly use?"
- "How does this relate to our method/finding/limitation?"

---

## Phase 5: Method Comparison (When Reviewing ≥2 Papers)

When the review involves comparing methods, produce a structured comparison:

### 5a. Comparison Table

| Dimension | Paper A | Paper B | ... |
|-----------|---------|---------|-----|
| Framework type | | | |
| Primary goal | | | |
| Key technical component | | | |
| How it handles [problem X] | | | |
| How it handles [problem Y] | | | |
| Computational complexity | | | |
| Evaluation metrics | | | |
| Datasets | | | |
| Strengths | | | |
| Limitations | | | |

### 5b. Key Differences Analysis
For each major difference between methods:
1. **What** differs (the specific design choice)
2. **Why** it matters (impact on results or applicability)
3. **Which is better for our use case** (with reasoning)

### 5c. Synthesis Recommendation
Conclude with:
- Which method (or combination) best fits the current project
- What to adopt vs. what to skip
- Specific implementation suggestions with parameter choices

---

## Phase 6: Synthesis & Output

### Structured Review Format
Group by **themes**, not chronologically:

```markdown
# Literature Review: [Topic]

## 1. [Theme A]
### Summary
[2-3 paragraph synthesis]

### Papers
| Paper | Year | Venue | Method | Key Finding | Relevance |
|-------|------|-------|--------|-------------|-----------|

## 2. [Theme B]
...

## Method Comparison (if applicable)
[Comparison table + key differences + recommendation]

## Gap Analysis
- What has been done
- What has NOT been done (our contribution fills this)
- Methodological limitations in existing work

## References
[Full citation list]
```

### Per-Paper Entry Format
```markdown
**[Authors] (Year). Title. Venue.**
- Problem: ...
- Method: ...
- Key findings: ...
- Relevance: [connection to our work]
- Limitations: ...
```

---

## Phase 7: Delivery

1. Save as Markdown in project docs directory
2. Export to PDF or HTML if requested
3. Email to user if requested
4. Push to Git repository
5. Update paper log if one exists

---

## Fallback Expansion

If main directions yield <20 references, expand in order:
1. Methodological justification (why this model family over alternatives)
2. Validity threats (confounding, external validity)
3. Adjacent application domains

---

## Important Rules

1. **Screen before deep-reading** — don't read 50 papers end-to-end
2. **Summarize only what you read** — no fabrication from abstracts
3. **Track search queries** — reproducibility of the review process
4. **Prioritize published over preprint** — solid 2024 paper > shaky 2026 preprint
5. **When comparing methods, be specific** — cite exact equations, kernel choices, architectural decisions
6. **Always include applicability assessment** — how does each paper's approach map to the user's problem
7. **Handle API failures gracefully** — if one source is 429'd, switch to next; don't burn tokens retrying
8. **Knowledge capital**: every paper read should produce a reusable asset (citable key points in own words + cross-paper connections), not just a checkmark

---

*Skill version: 2026-03-31 — Derived from Xavier's research workflow with Steven Hsu*
