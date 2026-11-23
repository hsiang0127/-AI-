# AGENTS.md — 論文 LLM Wiki 知識庫運作規範

本文件是此論文知識庫的 **Schema（運作規範）**，參考 Andrej Karpathy 的 LLM Wiki 模式設計。所有 LLM Agent 在處理本專案時，皆須嚴格遵守以下架構、命名、格式與工作流。

---

## 1. 專案目標與核心原則

- **定位**：跨領域論文閱讀與知識累積庫，搭配 **Obsidian** 瀏覽與探索（Graph View / Dataview），並為日後製作 **HTML 簡報** 預留結構化素材。
- **核心原則**：
  1. **累積而非重算**：知識編譯一次後持續更新與串聯，而非每次提問都重新掃描原始 PDF。
  2. **分工明確**：使用者負責選題、提供論文、指導分析方向與提問；Agent 負責檔案整理、筆記撰寫、跨頁串聯、矛盾標記與日常維護。
  3. **忠於來源**：所有重要論點、數據與定義皆須透過 `[[Wikilinks]]` 或來源欄位追溯至 `raw/` 中的原始論文，不可憑空捏造數據。
  4. **語言風格**：以**繁體中文**撰寫為主；**論文原標題、專有名詞、方法名稱、模型名稱、Benchmark 名稱**保留英文。

---

## 2. 目錄結構與權限邊界

```text
LLM Wiki Notes/
├── AGENTS.md                 # 本文件：知識庫 Schema（與使用者共同演進）
├── assets/                   # [唯讀／保留不動] 未來 HTML 簡報用素材，維護 Wiki 時切勿修改或刪除
├── raw/                      # Layer 1：原始來源層（Immutable，存入後僅讀取、絕不修改）
│   ├── papers/               # 原始論文 PDF（含從對話接收或從 arXiv 等連結下載的 PDF）
│   └── clips/                # 網頁剪藏、部落格解讀、訪談逐字稿等輔助原始資料
└── wiki/                     # Layer 2：知識庫層（由 Agent 全權建立與維護）
    ├── index.md              # 內容導向總目錄（每次新增或更名頁面皆須同步更新）
    ├── log.md                # 時間軸操作紀錄（Append-only，記錄 ingest / query / lint）
    ├── papers/               # 單篇論文筆記
    ├── concepts/             # 核心概念、理論、問題定義、跨領域思維模型
    ├── methods/              # 具體方法、演算法、系統架構、代表性模型
    ├── benchmarks/           # 評測基準、資料集、評估指標
    └── syntheses/            # 跨論文比較、主題綜述、研究脈絡、經確認沉澱的問答分析
```

> **擴充預留**：目前維持 `papers` / `concepts` / `methods` / `benchmarks` / `syntheses` 五大類。待論文累積達 20～30 篇以上、確實需要獨立追蹤特定研究團隊或作者譜系時，再與使用者討論是否新增 `entities/`。

---

## 3. 原始資料處理規則（Raw Sources）

1. **使用者在對話中提供 PDF**：將 PDF 複製／儲存至 `raw/papers/`，並依命名規則重新命名（見第 4 節）。
2. **使用者提供 arXiv 或其他線上論文連結**：主動下載該論文的 PDF（例如將 `arxiv.org/abs/<id>` 轉為 `arxiv.org/pdf/<id>.pdf` 下載），存入 `raw/papers/`。若下載受阻，主動回報使用者並請其協助提供或確認。
3. **使用者提供網頁文章或文字剪藏**：存為 `.md` 或 `.txt` 放入 `raw/clips/`。
4. **不可變原則（Immutable）**：檔案存入 `raw/` 後即為 Source of Truth，**絕對不可修改其內容**。

---

## 4. 命名規則與連結規範

### 4.1 檔名規則
在 Obsidian 中 `[[Wikilinks]]` 會跨資料夾比對檔名，因此所有頁面檔名須保持唯一且簡潔：

| 類別 | 存放位置 | 命名格式 | 範例 |
| :--- | :--- | :--- | :--- |
| 原始論文 PDF | `raw/papers/` | `FirstAuthorYYYY-ShortTitle.pdf` | `Vaswani2017-Attention.pdf` |
| 單篇論文筆記 | `wiki/papers/` | `FirstAuthorYYYY-ShortTitle.md` | `Vaswani2017-Attention.md` |
| 概念頁 | `wiki/concepts/` | `<English-Term>.md` | `In-Context-Learning.md` |
| 方法／模型頁 | `wiki/methods/` | `<English-Name>.md` | `LoRA.md`, `Chain-of-Thought.md` |
| 評測／資料集頁 | `wiki/benchmarks/` | `<Benchmark-Name>.md` | `MMLU.md`, `SWE-bench.md` |
| 綜述／比較頁 | `wiki/syntheses/` | `<Topic-Slug>.md` | `Agent-Memory-Comparison.md` |

- 檔名避免使用特殊符號（`:` `/` `\` `?` `*` `"` `<` `>` `|`），詞彙間以連字號 `-` 連接。
- 若同一一作同一年有多篇論文，於短標題區分（如 `Wei2022-CoT.md`、`Wei2022-Emergent.md`）。

### 4.2 連結規範（Obsidian Graph View）
- Wiki 內部交叉引用一律使用 `[[檔名]]` 或 `[[檔名|顯示文字]]`。
- **重視連結品質**：只對真正具語意關聯的概念、方法、基準與相關論文建立雙向連結；避免對過於泛用的詞（如「神經網路」「實驗」）過度建頁或亂連。
- **消除孤立頁**：每篇新建的頁面都必須至少連向一個既有概念／方法／綜述，並在對應頁面或 `index.md` 中被連結。

---

## 5. YAML Frontmatter 與頁面模板

所有 `wiki/` 下的頁面（`index.md` 與 `log.md` 除外）皆須包含標準化 YAML Frontmatter，以支援 Obsidian **Dataview** 查詢。

### 5.1 單篇論文筆記模板（`wiki/papers/FirstAuthorYYYY-ShortTitle.md`）

```markdown
---
type: paper
title: "<論文完整英文標題>"
authors: ["<First Author>", "<Second Author>", "..."]
year: 2024
venue: "<arXiv / NeurIPS / ICLR / ACL / ...>"
domain: ["<領域標籤，如 llm-agents, hci, systems>"]
tags: ["<細部關鍵字>"]
source_pdf: "raw/papers/FirstAuthorYYYY-ShortTitle.pdf"
source_url: "<arXiv 或論文網址，無則留空>"
status: processed
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# <論文完整英文標題>

> **一句話摘要（TL;DR）**：用 1～2 句話點出這篇論文解決了什麼問題、核心方法與最大貢獻。

## 1. 研究問題與背景（Problem & Motivation）
- 現有方法面臨的核心痛點或限制是什麼？
- 與既有概念 [[Concept-A]] 的關聯。

## 2. 核心方法（Key Method）
- 說明方法的核心直覺、架構流程與關鍵設計。
- 連結至對應方法頁：[[Method-X]]。

## 3. 實驗設定與主要結果（Experiments & Results）
- 使用的評測基準：[[Benchmark-Y]]。
- 關鍵量化結果、消融實驗（Ablation）發現與對比基線。

## 4. 限制、爭議與批判（Limitations & Critique）
- 作者自述的限制 + 我們觀察到的盲點或適用邊界。
- 若與既有筆記存在觀點衝突，明確標註 `⚠️ 衝突：` 並連結相關頁面。

## 5. 跨論文關聯（Connections）
- **承襲自 / 延伸**：[[PriorPaper]]
- **對比 / 替代方案**：[[CompetingPaper]]
- **跨領域啟發**：（這篇的方法或發現能否遷移到其他領域？）

## 6. 簡報可用亮點（Presentation Hooks）
- **核心賣點（Hook）**：適合放在簡報開場或結尾的一句話金句。
- **值得截圖的圖表**：例如 `Fig. 2（系統總架構圖，適合講解流程）`、`Table 1（主實驗對比）`。
- **最亮眼數字 / Takeaway**：1～3 個最適合在簡報上放大的關鍵數據或結論。
```

### 5.2 概念／方法／評測頁模板（`concepts/`、`methods/`、`benchmarks/`）

```markdown
---
type: concept # 或 method / benchmark
title: "<英文專有名詞>"
aliases: ["<中文譯名或縮寫>"]
domain: ["<領域標籤>"]
tags: ["<關鍵字>"]
sources: ["[[FirstAuthorYYYY-ShortTitle]]"]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# <英文專有名詞>（<中文譯名>）

## 定義與核心直覺
（簡明清楚地解釋這個概念／方法／基準是什麼、為什麼重要。）

## 演進脈絡與關鍵變體
- 由 [[PaperA]] 提出，後續 [[PaperB]] 在 …… 上做出改進。

## 優缺點與適用場景
- **優勢**：……
- **限制**：……

## 跨論文觀點與矛盾
- （若不同論文對此有不同結論或實證結果，集中紀錄於此。）

## 相關頁面
- 相關概念：[[Related-Concept]]
- 來源論文：[[FirstAuthorYYYY-ShortTitle]]
```

### 5.3 綜述／比較頁模板（`wiki/syntheses/<Topic-Slug>.md`）

```markdown
---
type: synthesis
title: "<主題標題>"
domain: ["<領域標籤>"]
tags: ["<關鍵字>"]
sources: ["[[PaperA]]", "[[PaperB]]"]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# <主題標題>

## 核心結論
（先講結論與洞察。）

## 跨論文比較
| 論文 | 核心方法 | 優勢 | 限制 | 關鍵指標 |
| :--- | :--- | :--- | :--- | :--- |
| [[PaperA]] | ... | ... | ... | ... |

## 延伸分析與未解問題（Open Questions）
- ……
```

---

## 6. 核心工作流（Operations）

### 6.1 Ingest（吸收新論文）— 兩階段互動式

當使用者提供新論文（PDF、arXiv 連結或文字）並要求吸收時，嚴格執行以下兩階段：

#### Stage 1：收檔、閱讀與討論（不直接改動 Wiki）
1. **歸檔原始來源**：
   - 對話傳入 PDF → 存入 `raw/papers/FirstAuthorYYYY-ShortTitle.pdf`。
   - 提供 arXiv 等連結 → 下載 PDF 並存入 `raw/papers/FirstAuthorYYYY-ShortTitle.pdf`。
   - 網頁／文字剪藏 → 存入 `raw/clips/`。
2. **深度閱讀**：完整閱讀論文內容（包含摘要、方法、主要實驗與結論），並先查看 `wiki/index.md` 掌握現有知識庫脈絡。
3. **向使用者回報討論草稿**：
   - 核心摘要（3～5 點）與創新點。
   - 預計**新建**的頁面清單（如 `papers/` 與必要的新概念／方法／基準頁）。
   - 預計**更新**的既有頁面清單（以及與現有筆記的關聯或潛在矛盾）。
   - 詢問使用者：有沒有特別想強調的角度、切入點，或針對未來簡報想特別留意的重點？
4. **等待使用者確認後，再進入 Stage 2。**（除非使用者明確指示「直接寫入不用討論」。）

#### Stage 2：寫入與全面串聯
獲得使用者確認後，一次完成：
1. 建立 `wiki/papers/FirstAuthorYYYY-ShortTitle.md`（含 `簡報可用亮點`）。
2. 新建或更新相關的 `concepts/`、`methods/`、`benchmarks/`、`syntheses/` 頁面，確保雙向 `[[Wikilinks]]` 與 `sources`、`updated` 欄位同步更新。
3. 更新 `wiki/index.md`（將新頁面歸類並寫上精簡的一句話描述）。
4. 在 `wiki/log.md` 末端追加一筆 `ingest` 紀錄。

---

### 6.2 Query（查詢與綜合分析）

當使用者針對知識庫提問時：
1. 先閱讀 `wiki/index.md`，快速鎖定相關頁面，再讀取對應的 `wiki/` 頁面進行綜合推理（必要時才回查 `raw/` 確認原始細節）。
2. 回答須附帶明確的 `[[Wikilinks]]` 引用來源。
3. **寫回 Wiki 的觸發條件**：若該次回答產出了有價值的跨論文比較、主題綜述或新洞見，**由 Agent 主動提議是否存為 `wiki/syntheses/` 頁面**；**經使用者同意後**才寫入檔案，並同步更新 `index.md` 與 `log.md`。

---

### 6.3 Lint（知識庫健康檢查）

當使用者要求執行 `lint` 或定期維護時，Agent 應全面檢視 `wiki/` 並產出體檢報告，重點檢查：
1. **矛盾與過時資訊**：不同論文對同一概念／方法是否有矛盾結論卻未標註？是否有舊結論已被新論文推翻？
2. **孤立頁面（Orphans）**：沒有任何入站 `[[Wikilinks]]` 的頁面。
3. **缺頁概念（Missing Pages）**：在多篇論文中被頻繁提及、值得獨立成頁，但目前尚未建立的概念、方法或 Benchmark。
4. **斷裂連結與索引同步**：檢查是否有失效的 `[[Wikilinks]]`，以及 `wiki/index.md` 是否漏列或與實際檔案不同步。
5. **Frontmatter 一致性**：檢查必填欄位（`type`、`domain`、`sources`、`updated`）是否齊全。
6. 檢查完成後，先呈現問題清單與修復建議，經使用者同意後執行修正，並在 `wiki/log.md` 追加一筆 `lint` 紀錄。

---

### 6.4 Presentation（教學型 HTML 簡報生成 Graph）

從 `wiki/` 產出教學型 HTML 簡報時，遵循「知識層與呈現層分離、檢查往前移（Shift-Left）」的 Graph 工作流：
1. **N0 取材 → N1 大綱規劃 → Gate G1（人工＋AI 檢核）**：檢查零基礎可讀性、單頁單重點與邏輯連貫，不通過則回退 N1。
2. **N2 逐頁腳本撰寫 → Gate G2（事實與教學密度檢核，人工確認）**：每頁須標註對應 `[[Wikilinks]]` 來源與白話類比；若數據缺依據則觸發 **N2A** 回查 `wiki/papers/` 補證，字數過密則回退 N2。
3. **N3 視覺與素材準備（`assets/`）→ N4 生成 HTML 簡報 → Gate G3（實機渲染與來源對應檢核）**：檢查 16:9 視窗不溢位、互動切換正常、風格一致（日式文青風格 + 細線 SVG 圖解 + `assets/` 插畫）；依問題類型精準回退 N4（CSS）、N3（圖表素材）或 N2（敘事）。
4. **N5 交付與紀錄**：將檢核發現與修正過程追加至 `wiki/log.md`。

---

## 7. 索引與日誌規範

### 7.1 `wiki/index.md`（內容導向總目錄）
- 依五大分類（Papers / Concepts / Methods / Benchmarks / Syntheses）列出所有頁面，每條格式為：
  `- [[檔名]] — 一句話摘要（\`domain\`）`
- 頂部維護一份「領域總覽（Domains）」，方便快速掌握目前涵蓋了哪些跨領域主題。

### 7.2 `wiki/log.md`（時間軸紀錄）
- 採 **Append-only**，每筆紀錄以固定格式開頭，方便日後以指令檢索：
  ```markdown
  ## [YYYY-MM-DD] <ingest | query | lint | init> | <簡短標題>
  - **動作**：簡述做了什麼
  - **異動頁面**：[[Page1]], [[Page2]]
  ```

---

## 8. Schema 共同演進

本文件並非一成不變。隨著論文數量增加、領域擴展，或日後開始使用 `assets/` 製作 HTML 簡報時，若發現現有模板、分類或工作流需要調整，Agent 可提出具體改進建議，經使用者同意後共同修訂 `AGENTS.md`。
