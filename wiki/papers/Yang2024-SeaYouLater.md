---
type: paper
title: "Sea You Later: Metadata-Guided Long-Term Re-Identification for UAV-Based Multi-Object Tracking"
authors: ["Cheng-Yen Yang", "Hsiang-Wei Huang", "Zhongyu Jiang", "Heng-Cheng Kuo", "Jie Mei", "Chung-I Huang", "Jenq-Neng Hwang"]
year: 2024
venue: "WACV Workshops (MaCVi 2024)"
domain: ["uav-tracking", "computer-vision", "multi-object-tracking"]
tags: ["uav-metadata", "world-coordinate-tracking", "long-term-reid", "seadronessee", "bot-sort"]
source_pdf: "raw/papers/Yang2024-SeaYouLater.pdf"
source_url: "https://arxiv.org/abs/2311.03561"
status: processed
created: 2026-11-23
updated: 2026-11-23
---

# Sea You Later: Metadata-Guided Long-Term Re-Identification for UAV-Based Multi-Object Tracking

> **一句話摘要（TL;DR）**：針對海面無人機追蹤中目標微小、外觀高度相似且頻繁進出視野的長時程重辨識難題，提出 [[MG-MOT]]，利用 UAV 逐幀元資料（GPS、航高、雲台姿態）將 2D 偵測投影至 3D 海面世界座標，搭配雙向運動外插（BiME）與時間門檻擴張（MTE），奪下 2024 MaCVi [[SeaDronesSee]] 競賽第一名（HOTA 69.5%、IDF1 85.9%）。

## 1. 研究問題與背景（Problem & Motivation）
- 在海面 [[UAV-Multi-Object-Tracking]] 任務中（如搜救場景），目標（泳客、船隻）尺寸極小、易受浪花與反光遮擋，且外觀幾乎一致，導致傳統依賴外觀特徵的 Re-ID 完全失效。
- 無人機快速飛行或雲台急轉（pitch / yaw）會造成兩種斷軌：(1) **短期斷軌**：目標仍在畫面內但因劇烈位移導致 2D IoU 關聯中斷；(2) **長期重辨識（Long-Term Re-ID）**：目標短暫離開相機視野後再次出現，必須恢復原本 ID。

## 2. 核心方法（Key Method）
[[MG-MOT]] 以 `YOLOv8-x`（加 `p2` 小目標偵測頭並與 COCO 預訓練模型整合）+ `BoT-SORT`（含稀疏光流 [[Camera-Motion-Compensation|GMC]]）為基礎，核心在於**元資料引導的世界座標投影與重辨識**：
1. **估計相機模型與 3D 海面投影**：
   - 利用靜止目標序列校準相機視角 $fov = 70^\circ$（解析度 $3840 \times 2160$），以雲台航向角 $\alpha = 90^\circ - \text{gimbal\_heading}$、俯仰角 $\beta = \text{gimbal\_pitch}$（假設滾轉角為 0）建立旋轉矩陣 $R(\alpha, \beta, 0)$。
   - 將 2D 框中心射線 $\text{ray}_i = R K^{-1} \frac{x_i}{\|x_i\|}$ 與海平面（$z = 0$）求交，結合無人機 GPS 與航高 $Z_{drone}$ 得 3D 世界座標：
     $$X_i^{3D} = Loc_{drone} + \frac{Z_{drone}}{Z_{\text{ray}_i}} \cdot \text{ray}_i$$
2. **短期 Re-ID（Short-Term Re-ID）**：在 3D 世界座標上直接以歐氏距離 $\|X_{t_i^{exit}}^{3D} - X_{t_j^{entry}}^{3D}\|_2$ 執行匈牙利配對，修補因雲台急轉斷裂的軌跡。
3. **長期 Re-ID（Long-Term Re-ID）**：針對離開視野再出現的目標，加入兩項機制：
   - **雙向運動外插（BiME）**：分別由離開前與進入後窗口 $w$ 估計 3D 速度並做前向／後向外插（外插時間受記憶上限 $\tau_{\text{memory}}$ 限制）。
   - **匹配門檻擴張（MTE）**：隨消失與重現的時間間隔線性放大空間匹配門檻：$\tau'_{\text{match}} = \lambda (\Delta t_{exit} + \Delta t_{entry}) \tau_{\text{match}}$。

## 3. 實驗設定與主要結果（Experiments & Results）
- **評測基準**：2024 MaCVi [[SeaDronesSee]] MOT with Re-ID 測試集（19 段影片、18,253 幀）。
- **主實驗表現（Table 3）**：**HOTA 69.5%**（第一名）、**IDF1 85.9%**（第一名）、**MOTA 77.9%**、Precision **89.7%**、IDs 僅 **18**（MaCVi 官方基線為 HOTA 37.5%、IDF1 38.8%、IDs 1340）。
- **關鍵消融（Table 4，Testing Split）**：
  - `BoT-SORT` 基線：HOTA `60.3` / IDF1 `68.2` / IDs `84`。
  - 加入 Short Re-ID：HOTA `64.0` / IDF1 `76.6`。
  - 加入 Long Re-ID：HOTA `69.0` / IDF1 `85.4` / IDs 暴降至 `26`（**+8.7 HOTA、+17.2 IDF1**）。
  - 完整 `MG-MOT`（Short + Long + Detector Ensemble）：HOTA **`69.5`** / MOTA **`77.9`** / IDs **`18`**。
- **偵測器消融（Table 5）**：加入 `p2` 小目標頭、改用無損 `png` 並提高解析度至 `1280`，使 MOTA 從 `64.3` 大幅提升至 **`77.3`**。

## 4. 限制、爭議與批判（Limitations & Critique）
- **元資料誤差與時間不同步（作者自述，Sec. 5.4, Fig. 3）**：真實測試集中出現 `gimbal_pitch` 標示 `90.0°` 卻實際傾斜約 `45°`、或雲台航向角延遲不同步的異常，直接導致 3D 世界座標嚴重漂移——這正是未建模 [[Geolocation-Uncertainty]] 的致命傷！
- **平坦海面與零滾轉假設**：假設地表為平坦 $z=0$ 且無滾轉角，無法直接適用於具地形起伏的陸地航測（需改用 DSM）。
- **固定線性外插與無動靜區分**：BiME 假設離開視野期間維持等速直線運動，且直接以固定距離門檻 $\tau_{\text{match}}$ 配對，未區分「定位漂移造成的假位移」與「真實低速運動或啟停轉換」，在相近物件啟停時易發生誤接。

## 5. 跨論文關聯（Connections）
- **承襲自 / 延伸**：以 `BoT-SORT` 與 `YOLOv8` 為前端，開創「UAV 元資料 → 世界座標 Re-ID」路線。
- **對比 / 替代方案**：
  - 與 [[Yi2024-UCMCTrack]] 互補：MG-MOT 有真實 UAV 感測器外方位但只算歐氏距離、忽略誤差協方差；UCMCTrack 建立了投影協方差（誤差橢圓）與馬氏距離，但缺乏即時感測器元資料與地形高程。
  - 與 [[Ma2026-AMOT]] 對比：AMOT 靠密集外觀回應圖解 2D 視角變化；MG-MOT 針對外觀幾乎無辨識度的海面小目標，改以純幾何世界座標突圍。
- **對本研究規劃的啟發**：直接驗證「UAV 直接定位資訊投影至世界座標」能大幅提升跨視角重辨識（IDF1 +17.2%），但也暴露了未校準元資料漂移與啟停誤接的缺口（見 [[UAV-Tracking-Geolocation-Synthesis]]）。

## 6. 簡報可用亮點（Presentation Hooks）
- **核心賣點（Hook）**：海面上的泳客長得都一樣、無人機一轉頭就跟丟？改用無人機自帶的 GPS 與雲台姿態把目標投到「3D 世界地圖」上找，IDF1 直接從 **68.2% 飆升到 85.9%**！
- **值得截圖的圖表**：
  - `Fig. 1`（p.2，UAV 快速移動、雲台急轉、目標重入畫面三大失敗情境，最適合簡報開場講痛點）。
  - `Fig. 2`（p.3，相機射線與海平面求交示意圖，清楚講解 2D → 3D 世界座標轉換）。
  - `Fig. 3`（p.6，真實測試集中的 `gimbal_pitch` 元資料錯誤案例 — **完美鋪陳「為何必須考量定位不確定性」的轉折頁！**）。
- **最亮眼數字 / Takeaway**：2024 MaCVi 競賽 **第 1 名**；加入世界座標 Long-Term Re-ID 後，IDF1 暴漲 **+17.2%**（68.2 → 85.4），ID 切換由 **84 次驟降至 18 次**。
