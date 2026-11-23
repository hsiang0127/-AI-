# LLM Wiki Operation Log

> 時間軸操作紀錄（Append-only）。每筆紀錄皆以 `## [YYYY-MM-DD] <operation> | <title>` 開頭，方便檢索與追蹤知識庫演進。

## [2026-11-23] init | 建立論文 LLM Wiki 知識庫架構
- **動作**：完成 `AGENTS.md` Schema 定義，並建立 `raw/` 與 `wiki/` 初始目錄結構、`index.md` 及 `log.md`。
- **異動頁面**：`AGENTS.md`, `wiki/index.md`, `wiki/log.md`

## [2026-11-23] ingest | 切換主題為無人機間隔航測追蹤並批次吸收 5 篇核心論文
- **動作**：依使用者碩士研究規劃（歸檔於 `raw/clips/Master-Research-Proposal.md`）定調主題，從 arXiv 下載 5 篇核心論文 PDF 至 `raw/papers/`，完成深度閱讀並建立 5 篇論文筆記、5 個核心概念頁、5 個方法頁、4 個評測基準頁與 1 篇跨論文綜述頁，同步更新 `wiki/index.md`。
- **異動頁面**：[[Yang2024-SeaYouLater]], [[Yi2024-UCMCTrack]], [[Liao2024-MoD2T]], [[Ma2026-AMOT]], [[Lindenberger2023-LightGlue]], [[UAV-Multi-Object-Tracking]], [[Geolocation-Uncertainty]], [[Camera-Motion-Compensation]], [[Motion-State-Estimation]], [[Local-Feature-Matching]], [[MG-MOT]], [[UCMCTrack]], [[MoD2T]], [[AMOT]], [[LightGlue]], [[SeaDronesSee]], [[VisDrone]], [[UAVDT]], [[Motion-State-Benchmark]], [[UAV-Tracking-Geolocation-Synthesis]], `wiki/index.md`

## [2026-11-23] lint | 首次知識庫健康檢查（5 篇論文建置後）
- **動作**：執行自動化腳本全面檢視 `wiki/` 下 22 個 Markdown 檔案之 `[[Wikilinks]]` 完整性、孤立頁（Orphans）、`index.md` 同步狀態、Frontmatter 欄位與跨論文矛盾標註。結果：斷裂連結 `0`、孤立頁 `0`；並確認兩處跨論文實證衝突（1. [[Camera-Motion-Compensation]] 在 MOT17 加分但在 KITTI 低幀率劇烈運動下反扣分；2. [[UAV-Multi-Object-Tracking]] 中外觀特徵在密集城市空拍有效但在海面微目標失效）皆已明確標註。
- **異動頁面**：`wiki/log.md`（檢查全數通過，無需修補）

## [2026-11-23] presentation | 依 Graph 工作流完成 14 頁日式文青風教學 HTML 簡報（含 G1/G2/G3 檢核與回修）
- **動作**：
  - **N0 → N1 → Gate G1**：完成 13 頁零基礎大綱，經使用者於 G1 審核新增第 14 頁（LLM Wiki + Graph 製作流程），定案為 14 頁。
  - **N2 → Gate G2**：撰寫逐頁腳本 [[Presentation-Script-UAV-Tracking]] 並回查 5 篇 `wiki/papers/` 核實所有數據（`61.4%`、`31.4 ms`、`85.9%`、`>1000 FPS`、`0.827 → 0.230`），確認零幻覺與教學密度適中後通過使用者 G2 確認。
  - **N3 → N4**：生成 3 張日式水彩版畫風插畫存入 `assets/`（`cover-uav-washi.jpg`、`section-world-map.jpg`、`closing-washi.jpg`），搭配 9 張細線 SVG 原理圖產出互動式簡報 `slides.html`。
  - **Gate G3（抓出真實問題並回退 N4 修正）**：自動化檢核發現 8 處從 Markdown 腳本轉寫進 HTML 時殘留的原始 LaTeX `$...$` 與反引號語法，以及 2 處跨 SVG `<defs>` 箭頭依賴；立即回退 **N4** 改寫為標準 HTML 排版（`<i>`/`<sub>`/`<sup>`/`<code>`）與獨立 SVG `<defs>`，重測通過（殘留語法 `0`、缺圖 `0`、14 頁完整對應 Wiki）。
- **異動頁面**：`AGENTS.md`, [[Presentation-Script-UAV-Tracking]], `wiki/index.md`, `slides.html`, `assets/cover-uav-washi.jpg`, `assets/section-world-map.jpg`, `assets/closing-washi.jpg`
