---
type: paper
title: "Tracking the Unstable: Appearance-Guided Motion Modeling for Robust Multi-Object Tracking in UAV-Captured Videos"
authors: ["Jianbo Ma", "Hui Luo", "Qi Chen", "Yuankai Qi", "Yumei Sun", "Amin Beheshti", "Jianlin Zhang", "Ming-Hsuan Yang"]
year: 2026
venue: "AAAI 2026"
domain: ["uav-tracking", "computer-vision", "multi-object-tracking"]
tags: ["uav-mot", "appearance-motion-consistency", "track-continuation", "jde", "reid"]
source_pdf: "raw/papers/Ma2026-AMOT.pdf"
source_url: "https://arxiv.org/abs/2508.01730"
status: processed
created: 2026-11-23
updated: 2026-11-23
---

# Tracking the Unstable: Appearance-Guided Motion Modeling for Robust Multi-Object Tracking in UAV-Captured Videos

> **一句話摘要（TL;DR）**：針對無人機不穩定視角與劇烈位移問題，提出免訓練、即插即用的 [[AMOT]] 框架，透過「外觀引導的雙向空間一致性（AMC）」與「運動感知軌跡延續（MTC）」，在無需額外學習式運動模型下大幅降低 ID 切換並維持 36.4 FPS 即時效能。

## 1. 研究問題與背景（Problem & Motivation）
- 在 [[UAV-Multi-Object-Tracking]] 場景中，無人機頻繁的視角變化與機體－目標相對運動，造成目標外觀劇變與非線性大位移。
- 現有方法通常將「運動模型（如 Kalman Filter + [[Camera-Motion-Compensation|CMC]]）」與「外觀模型（ReID 餘弦距離）」分開建立代價矩陣後再融合；當視角突變使運動預測失效、或小目標外觀過於相似時，兩者產生衝突導致關聯崩潰。
- 傳統追蹤嚴格依賴「偵測框與軌跡」的配對，一旦發生短暫漏偵測（missed detection），軌跡就會中斷並產生大量 ID 切換（IDs）。

## 2. 核心方法（Key Method）
建立於 Joint Detection and Embedding（JDE，骨幹為 DLA-34、輸出 $152 \times 272$ 密集 ReID 特徵圖 $\mathbf{E}^t$）架構之上，在推論階段引入兩個**免訓練、即插即用**模組（詳見 [[AMOT]]）：
1. **Appearance-Motion Consistency（AMC）矩陣**：
   - **前向密集回應圖**：用前一幀軌跡 $j$ 的 ReID 向量去比對當前幀整張特徵圖 $\mathbf{E}^t$，取最大值位置 $\mathcal{Q}_{trk}^{(j)}$ 與實際偵測中心算前向距離 $\mathbf{D}_f$。
   - **後向密集回應圖**：反過來用當前偵測 $i$ 的 ReID 向量去比對前一幀特徵圖 $\mathbf{E}^{t-1}$，取最大值位置 $\mathcal{Q}_{det}^{(i)}$ 算後向距離 $\mathbf{D}_b$。
   - 兩者相加經高斯核（$\sigma = 5$）轉為一致性代價 $\mathbf{C}_{AMC}$，再與 $\mathbf{C}_{IOU}$、$\mathbf{C}_{App}$ 組成統一第一階段代價矩陣：
     $$\mathbf{C}_{uni} = 1 - (1 - \mathbf{C}_{AMC}\cdot \mathbf{C}_{IOU})\cdot(1 - \mathbf{C}_{App})$$
2. **Motion-aware Track Continuation（MTC）**：
   - 在 BYTE 兩階段關聯後仍未配對的軌跡，若其最近 20 幀 FIFO 緩衝區內具連續狀態，則同時計算 Kalman Filter 預測中心 $\mathbf{c}_{kf}$ 與外觀密集回應圖峰值 $\mathbf{c}_{reid}$。
   - 當兩者距離 $d_k < \lambda$（$\lambda = 3$）且不與現有偵測大幅重疊時，**無須偵測框即主動延續軌跡**，避免短暫漏偵測造成的斷軌。

## 3. 實驗設定與主要結果（Experiments & Results）
- **評測基準**：[[VisDrone]]（VisDrone2019 test）、[[UAVDT]]（test）、VT-MOT-UAV（test）。*（註：本論文報告 IDF1 / MOTA / MT / ML / IDs / FPS，未報告 HOTA。）*
- **主實驗表現**：
  - **VisDrone2019**：IDF1 **61.4%**、MOTA **46.0%**、MT 716、IDs 1063、**36.4 FPS**（超越 DroneMOT 58.6% IDF1 與 ByteTrack 37.0% IDF1）。
  - **UAVDT**：IDF1 **74.7%**、MOTA **55.1%**、IDs 272（超越 STCMOT 69.8% IDF1）。
  - **VT-MOT-UAV**：IDF1 **52.7%**、MOTA **31.8%**、IDs 412。
- **消融實驗（Table 4，Baseline = FairMOT + BYTE）**：
  - VisDrone2019：Baseline（54.4 IDF1 / 3847 IDs）→ +AMC（60.5 / 1078）→ +AMC+MTC（**61.4** / **1063**，IDF1 **+7.0%**、IDs 減少 **2784**，速度僅降 0.7 FPS）。
  - 即插即用泛化（Table 6）：套用到 UAVMOT 與 STCMOT 上，在 VisDrone2019 分別帶來 **+4.4%** 與 **+7.0%** IDF1 提升。
  - 幀間隔壓力測試（Fig. 5 & Fig. 9）：在幀間隔拉大到 10 幀（車輛平均位移達 14 px）時，AMOT 的 IDF1 衰減遠小於傳統 IoU/KF 基線。

## 4. 限制、爭議與批判（Limitations & Critique）
- **純 2D 影像座標系限制**：AMOT 未使用相機姿態、單應性（Homography）或地理座標，無法區分像素位移是來自**物件真實移動**還是**無人機自身視角移動／視差**，在 [[Motion-State-Estimation]] 任務中容易產生假運動。
- **稀疏間隔航測影像下的退化風險**：
  - AMC 對整張特徵圖取硬性全域 `arg max`，若在間隔航測（如 1–2 Hz、80% 重疊率）下目標離開視野或因大視角變化造成外觀畸變，`arg max` 容易鎖定到相似的靜止干擾物。
  - MTC 依賴 20 幀連續狀態緩衝與線性 KF 預測（門檻僅 3 個特徵像素），在稀疏曝光間隔與急轉彎下幾乎無法觸發。
- **架構綁定**：需要能輸出密集空間 ReID 特徵圖的 JDE 架構，無法直接套用於純運動追蹤器（如 [[UCMCTrack]]）。

## 5. 跨論文關聯（Connections）
- **承襲自 / 延伸**：結合 FairMOT（JDE 架構）與 ByteTrack 兩階段關聯思想。
- **對比 / 替代方案**：
  - 與 [[Yi2024-UCMCTrack]] 形成強烈對比：UCMCTrack 完全不用外觀，靠地面座標投影與誤差協方差解決相機劇烈運動；AMOT 則完全在 2D 影像空間靠外觀引導運動。
  - 與 [[Yang2024-SeaYouLater]] 對比：Sea You Later 利用 UAV 元資料轉到世界座標處理長時程 Re-ID。
- **對本研究規劃的啟發**：AMOT 證明「外觀與運動一致性」能有效延續未匹配軌跡，但在稀疏航測影像中必須結合 [[Geolocation-Uncertainty]] 與 [[Local-Feature-Matching]] 才能控制假運動與 ID 誤接（見 [[UAV-Tracking-Geolocation-Synthesis]]）。

## 6. 簡報可用亮點（Presentation Hooks）
- **核心賣點（Hook）**：不靠昂貴的學習式運動預測網路，只用「前向＋後向外觀回應圖雙向互驗」，就能在無人機劇烈晃動下把 ID 切換砍掉超過 7 成（3847 → 1063）。
- **值得截圖的圖表**：
  - `Fig. 1`（p.1，VisDrone2019 的 IDF1–MOTA–FPS 氣泡圖，視覺化精準度與即時性兼具）。
  - `Fig. 3`（p.4，前向 $\mathbf{D}_f$ 與後向 $\mathbf{D}_b$ 密集回應圖雙向一致性示意圖，最適合教學講解 AMC 直覺）。
  - `Fig. 5`（p.6，幀間隔 1～10 幀的 IDF1 衰減曲線，呼應間隔影像大位移挑戰）。
- **最亮眼數字 / Takeaway**：VisDrone2019 IDF1 **61.4%**（+7.0% vs. Baseline）、IDs 減少 **72.4%**，且額外延遲僅 **0.7 FPS**（維持 **36.4 FPS**）。
