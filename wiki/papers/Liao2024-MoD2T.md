---
type: paper
title: "Motion State: A New Benchmark Multiple Object Tracking"
authors: ["Liao Pan", "Yang Feng", "Wu Di", "Liu Bo", "Zhang Xingle"]
year: 2024
venue: "arXiv"
domain: ["uav-tracking", "computer-vision", "multi-object-tracking"]
tags: ["motion-state", "moving-vs-static", "background-modeling", "mvf1", "uavdt"]
source_pdf: "raw/papers/Liao2024-MoD2T.pdf"
source_url: "https://arxiv.org/abs/2312.17641"
status: processed
created: 2026-11-23
updated: 2026-11-23
---

# Motion State: A New Benchmark Multiple Object Tracking

> **一句話摘要（TL;DR）**：針對傳統多目標追蹤無法區分物件「相對地面是移動還是靜止」的盲點，提出模型與資料雙驅動的 [[MoD2T]] 框架（結合深度追蹤器、位移與背景相似度判斷、以及傳統雙模態高斯背景減除），並定義專屬評測指標 `MVF1` 與 [[Motion-State-Benchmark]]。

## 1. 研究問題與背景（Problem & Motivation）
- 在無人機監控、遙測與自動駕駛任務中，區分追蹤目標相對於地面為**移動（Moving）**或**靜止（Stationary）**至關重要，但現有 MOT 基準與方法僅依外觀一視同仁地追蹤所有目標。
- 單純仰賴單目 RGB 影像時，兩大陣營各有缺陷：
  1. **深度學習 MOT**：重外觀特徵、難以精確判斷相對地面的真實運動狀態。
  2. **傳統背景建模／運動偵測**（如高斯混合模型、幀差法）：具備物理運動線索，但框定位極差、物件靠近時容易沾黏合併，且在相機快速移動時完全崩潰。
- 參見核心概念 [[Motion-State-Estimation]]。

## 2. 核心方法（Key Method）
[[MoD2T]] 採「深度分支 + 傳統分支」雙路並行與自適應融合：
1. **深度分支的動靜判斷模組（Motion State Judgment）**：比較第 $t$ 幀與第 $t-n$ 幀（$n=3$），計算靜止分數：
   $$\Theta = \lambda A_a + (1 - \lambda) A_m$$
   （$\Theta$ 大於門檻判定為靜止，否則為移動）：
   - **位移線索 $A_m$**：先以 ECC 執行 [[Camera-Motion-Compensation]]，再以邊界框尺寸定義對角協方差 $\mathbf{C} = \text{diag}(w_1^2/4, h_1^2/4)$ 計算中心點馬氏距離 $D$，並以預設靜止上限 $\beta_d = 0.01\times\min(H, W)$ 歸一化。
   - **背景線索 $A_a$**：擷取目標周圍四個方向的背景區塊（縮放至 $256\times 192$），經預訓練 OSNet 抽特徵後以 SSIM 計算背景相似度（若物件移動，其周圍背景變化會很大）。
2. **傳統分支與有效性判別（Algorithm Effectiveness Judgment）**：
   - 使用具相機運動補償的改良雙模態單高斯背景模型（SGM）+ 形態學處理 + OC-SORT 產生前景運動像素遮罩。
   - **序列級與目標級失效檢查**：若背景前景像素占比過高（相機劇烈移動）或單一傳統框內的連通運動區域數 $n \ge 2$（相鄰目標沾黏或碎裂），即判定傳統分支失效，改僅用深度框並以框內前景像素比例（門檻 $R = 0.2$）輔助融合 $\Theta$。
3. **新評測指標 `MVF1`**：
   - 結合動靜分類精確率 $MVP$ 與考量漏偵測的召回率 $MVR = \frac{MVTP}{MVTP + BF\cdot MVFN}$，其中平衡因子 $BF = \frac{1}{2}(\text{Precision} + \text{Recall})$，避免漏偵測被忽略（$BF=0$）或過度懲罰（$BF=1$）。

## 3. 實驗設定與主要結果（Experiments & Results）
- **評測基準**：在 KITTI（3D 座標推導真值，分慢速／快速相機）、MOT17（人工補標動靜）與 [[UAVDT]]（無人機車輛，人工補標動靜）上建立 [[Motion-State-Benchmark]]。
- **動靜判斷結果（`MVF1`，Tables I–III）**：
  - **UAVDT**：僅深度位移 $A_m$（$\lambda=0$）為 0.438 → 加入背景 $A_a$（$\lambda=\text{best}$）升至 0.525 → 融合傳統分支的 **`MoD2T(DeepOCSORT)` 躍升至 0.827**（+30.2%）。
  - **KITTI（靜止／慢速相機）**：`DeepOCSORT+MJ` 0.472 → **`MoD2T(StrongSORT)` 0.775**。
  - **KITTI（快速相機）**：`MoD2T` 驟降至 **0.230～0.231**（幾乎與純深度 0.220 無異），暴露 2D ECC 與背景建模在劇烈相機運動下的失效。
  - **MOT17（行人）**：`MoD2T(DeepOCSORT)` 達 **0.521**（行人動靜明顯比車輛難判）。
- **對原追蹤品質的影響（Tables IV–VI）**：幾乎完全保留基底追蹤器的 HOTA/IDF1/MOTA，且在 UAVDT 上將 StrongSORT 的 AssA 由 `0.815` 提升至 **`0.896`**。
- **參數消融（Table IX）**：像素比例門檻 $R = 0.2$ 達最高 MVF1（`0.865`）。

## 4. 限制、爭議與批判（Limitations & Critique）
- **快速相機運動下嚴重崩潰**：2D ECC 無法補償 3D 視差，導致 KITTI 快速相機序列 MVF1 僅 `0.230`，傳統背景減除幾乎失效（MOTA 僅 `0.012`）。
- **缺乏機率式誤差傳播與「未確定」狀態**：
  - 位移協方差 $\mathbf{C}$ 僅依框寬高啟發式設定，未納入定位雜訊或相機對齊誤差；融合權重為資料集層級固定常數。
  - 僅輸出二元「移動／靜止」，未區分「證據不足的未確定狀態」，在定位不確定性高時容易把靜止誤判為移動，或漏判低速物件。
- **無啟停狀態機建模**：僅靠間隔 3 幀的單次門檻判斷，缺乏時序遲滯（hysteresis）與啟停轉換（stop-and-go）回饋追蹤的機制，臨界低速物件容易在動靜間反覆閃爍。
- **論文撰寫小瑕疵**：未公布 $\lambda=\text{best}$ 與 $\Theta$ 門檻數值，且附錄文獻 `[62]` 誤引成腫瘤學論文（原意應為 KLT 光流）。

## 5. 跨論文關聯（Connections）
- **承襲自 / 延伸**：整合 StrongSORT / DeepOCSORT（深度追蹤）與傳統雙模態 SGM 背景建模、OC-SORT。
- **對比 / 替代方案**：
  - 與 [[Yi2024-UCMCTrack]] 對比：UCMCTrack 透過地面投影解決相機運動，但不做動靜分類；MoD2T 明確判斷動靜，但受限於 2D 影像平面 ECC 補償。
- **對本研究規劃的啟發**：本論文點出了「相對地面動靜判斷」的核心需求，但直接揭示了「沒有地理定位不確定性建模、只做二元分類、不回饋給追蹤關聯」的三大缺口——這正是碩士研究規劃提出三態推論（移動／近似靜止／未確定）與啟停回饋關聯的出發點（見 [[UAV-Tracking-Geolocation-Synthesis]]）。

## 6. 簡報可用亮點（Presentation Hooks）
- **核心賣點（Hook）**：多目標追蹤不能只問「它是誰」，更要問「它到底有沒有在動」——結合周圍背景變化與傳統運動偵測，在無人機慢速場景能把動靜辨識率從 0.525 拉高到 **0.827**，但相機一快就掉到 **0.230**！
- **值得截圖的圖表**：
  - `Fig. 1` & `Fig. 2`（p.3–4，位移 $A_m$ 與四方向周圍背景相似度 $A_a$ 雙線索判斷流程，非常直覺）。
  - `Fig. 3`（p.5，MoD2T 雙分支架構與傳統分支失效自動切換邏輯）。
  - `Fig. 6`（p.11，UAVDT/KITTI 動靜紅藍框視覺化，對比慢速成功與快速失敗案例）。
- **最亮眼數字 / Takeaway**：UAVDT 動靜判斷 `MVF1` 達 **0.827**（+30.2% vs. 純深度），但快速相機場景驟降至 **0.230**——完美凸顯為何需要地理座標與不確定性建模！
