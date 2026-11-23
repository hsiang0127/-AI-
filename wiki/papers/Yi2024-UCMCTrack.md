---
type: paper
title: "UCMCTrack: Multi-Object Tracking with Uniform Camera Motion Compensation"
authors: ["Kefu Yi", "Kai Luo", "Xiaolei Luo", "Jiangui Huang", "Hao Wu", "Rongdong Hu", "Wei Hao"]
year: 2024
venue: "AAAI 2024"
domain: ["uav-tracking", "computer-vision", "multi-object-tracking"]
tags: ["camera-motion-compensation", "ground-plane-tracking", "mahalanobis-distance", "covariance-propagation", "kalman-filter"]
source_pdf: "raw/papers/Yi2024-UCMCTrack.pdf"
source_url: "https://arxiv.org/abs/2312.08952"
status: processed
created: 2026-11-23
updated: 2026-11-23
---

# UCMCTrack: Multi-Object Tracking with Uniform Camera Motion Compensation

> **一句話摘要（TL;DR）**：完全不依賴外觀特徵與逐幀影像配準，提出 [[UCMCTrack]]，將偵測框投影至地面座標並透過 Jacobian 誤差傳播建立旋轉相關誤差橢圓（CMD）與映射馬氏距離（MMD），搭配全序列統一的過程雜訊補償（UCMC），在單核 CPU 達成超過 1000 FPS 並於 MOT17/DanceTrack/KITTI 刷新純運動追蹤 SOTA。

## 1. 研究問題與背景（Problem & Motivation）
- 傳統 Tracking-by-Detection 在影像平面上使用定速 Kalman Filter 與 IoU 關聯；一旦發生劇烈相機運動、抖動、高速移動或低幀率，目標在影像上大幅偏移導致預測框與偵測框重疊為零（`IoU = 0`），關聯完全失效。
- 傳統解法（加入 ReID 外觀特徵，或逐幀執行 ECC / ORB / 光流等 [[Camera-Motion-Compensation]]）在高解析度影像上計算開銷龐大，阻礙即時應用。
- 既有地面座標追蹤方法（如 SparseTrack）仍依賴 IoU，且忽略了「從影像平面投影到地面平面時產生的非等向性定位不確定性（[[Geolocation-Uncertainty]]）」。

## 2. 核心方法（Key Method）
[[UCMCTrack]] 用「地面座標運動模型 + 投影不確定性建模」取代影像平面 IoU：
1. **地面投影與定速 Kalman Filter**：將偵測框底邊中點 $(u, v)$ 透過相機單應性矩陣 $\mathbf{A}$（由內外方位決定，假設平面高度 $z = z_0$）映射至地面座標 $(x, y)$，在地面上建立狀態 $\mathbf{x} = [x, \dot{x}, y, \dot{y}]^\top$。
2. **相關量測分布（Correlated Measurement Distribution, CMD）**：
   - 影像平面偵測雜訊為對角矩陣 $\mathbf{R}_k^{uv} = \text{diag}((\sigma_m w_k)^2, (\sigma_m h_k)^2)$（最佳 $\sigma_m = 0.05$）。
   - 透過 $\mathbf{A}^{-1}$ 的 Jacobian 矩陣 $\mathbf{C}$ 傳播至地面：$\mathbf{R}_k = \mathbf{C}\mathbf{R}_k^{uv}\mathbf{C}^\top$。因 $\mathbf{R}_k$ 非對角，形成具方向性的**旋轉誤差橢圓**，正確反映透視投影下遠近方向的誤差放大。
3. **統一相機運動補償（UCMC）與過程雜訊補償（PNC）**：
   - 捨棄逐幀影像配準，整個影片序列採用單組固定參數 $(\sigma_x, \sigma_y)$，將相機晃動視為地面運動模型的過程加速度雜訊 $\mathbf{Q}_k = \mathbf{G}\cdot\text{diag}(\sigma_x, \sigma_y)\cdot\mathbf{G}^\top$。
4. **映射馬氏距離（Mapped Mahalanobis Distance, MMD）**：
   - 取代 IoU，以殘差 $\boldsymbol{\epsilon} = \mathbf{z} - \mathbf{H}\hat{\mathbf{x}}$ 與殘差協方差 $\mathbf{S} = \mathbf{H}\mathbf{P}\mathbf{H}^\top + \mathbf{R}_k$ 計算歸一化距離：
     $$D = \boldsymbol{\epsilon}^\top \mathbf{S}^{-1}\boldsymbol{\epsilon} + \ln|\mathbf{S}|$$
   - 再以匈牙利演算法完成軌跡關聯。若疊加逐幀 ECC 補償則稱為 `UCMCTrack+`。

## 3. 實驗設定與主要結果（Experiments & Results）
- **評測基準**：MOT17、MOT20、DanceTrack、KITTI（註：本論文未評測 [[VisDrone]]）。
- **推論速度**：在提供偵測框條件下，單核 CPU 超過 **1000 FPS**。
- **主實驗表現**：
  - **MOT17-test**：`UCMCTrack` 達 **64.3 HOTA / 79.0 IDF1 / 79.0 MOTA**；`UCMCTrack+` 達 **65.8 HOTA / 81.1 IDF1 / 80.5 MOTA**（全面超越含外觀之 BoT-SORT 65.0 HOTA 與 Deep OC-SORT 64.9 HOTA）。
  - **DanceTrack-test**：`UCMCTrack` 達 **63.4 HOTA / 65.0 IDF1 / 51.1 AssA**（超越 Deep OC-SORT 61.3 HOTA，AssA 提升 **+5.5**）。
  - **KITTI-test（10 FPS 劇烈相機運動）**：Car 達 **77.1 HOTA / 90.4 MOTA**，Pedestrian 達 **55.2 HOTA / 67.4 MOTA**；且 `UCMCTrack` 反而勝過加了逐幀 CMC 的 `UCMCTrack+`（Car 74.2），證明低幀率劇烈晃動下傳統 CMC 參數不準反而有害。
- **關鍵消融（Table 4，MOT17-val / DanceTrack-val）**：
  - 單獨使用 MMD（未加 CMD 與 PNC）表現反比 IoU 低（DanceTrack HOTA `47.93 → 46.32`），因為少了邊界框高度資訊；但加入 **CMD** 後躍升至 `55.06`，再加 **PNC** 躍升至 **`60.42`**（IDF1 `47.27 → 62.64`）。
- **外方位誤差敏感度（Fig. 4c）**：偏航角（Y 軸）誤差影響極小，但俯仰／滾轉（X、Z 軸）誤差達 $3^\circ$～$6^\circ$ 時會造成地面嚴重變形與 HOTA 急劇下滑。

## 4. 限制、爭議與批判（Limitations & Critique）
- **單一平坦地面假設**：假設所有目標位於同一平面 $z = z_0$，在有坡度、高架橋或地形起伏的航測場景中會產生系統性高程投影誤差（這也是碩士研究規劃引入 DSM 的關鍵原因）。
- **全序列固定參數限制**：靜態與動態相機場景的最佳 $\sigma$ 截然不同（Fig. 4a），若單一航次中無人機姿態變化劇烈，固定參數難以全程最佳。
- **缺乏跨曝光誤差相關性校準**：UCMCTrack 僅將單次偵測雜訊透過 Jacobian 傳播至地面，未考量直接定位偏差、DSM 誤差與跨照片誤差交叉協方差 $C_{ij}$，也未將不確定性用於區分「真實移動 vs. 定位漂移」（見 [[Geolocation-Uncertainty]]）。

## 5. 跨論文關聯（Connections）
- **承襲自 / 延伸**：承襲 ByteTrack / OC-SORT 的追蹤框架，將影像平面 IoU 徹底升級為地面投影協方差與馬氏距離。
- **對比 / 替代方案**：
  - 與 [[Ma2026-AMOT]] 對比：AMOT 靠 2D 外觀回應圖應對不穩定視角；UCMCTrack 靠 3D/地面幾何與誤差橢圓，完全不用外觀。
  - 與 [[Yang2024-SeaYouLater]] 互補：Sea You Later 利用 UAV 實際感測器元資料（GPS/航高/姿態）投影至世界座標，補上了 UCMCTrack 缺乏即時外方位來源的缺口。
- **對本研究規劃的啟發**：提供「投影協方差（誤差橢圓）+ 馬氏距離」的數學基礎；本研究進一步引入 DSM 取代平坦地面假設，並校準跨曝光相對位移誤差與涵蓋率（見 [[UAV-Tracking-Geolocation-Synthesis]]）。

## 6. 簡報可用亮點（Presentation Hooks）
- **核心賣點（Hook）**：當鏡頭劇烈晃動讓影像框完全不重疊（`IoU = 0`）時，把框投影到地面並畫出「隨透視旋轉的誤差橢圓」，不用外觀模型、單核 CPU 就能跑超過 1000 FPS！
- **值得截圖的圖表**：
  - `Fig. 3`（p.4，三欄對比圖：(a) 影像 `IoU = 0` 失效 → (b) 未加 CMD 的圓形誤差誤配 → (c) 加上 CMD 的旋轉誤差橢圓精準配對 — **教學神圖！**）。
  - `Fig. 2`（p.3，完整系統流程圖）。
  - `Fig. 4c`（p.7，相機外方位 X/Y/Z 角度誤差對 HOTA 的影響曲線，直接呼應無人機姿態誤差問題）。
- **最亮眼數字 / Takeaway**：單核 CPU **>1000 FPS**；DanceTrack 關聯準確度 AssA **+5.5**；消融證實加入 CMD 誤差橢圓與 PNC 後 DanceTrack IDF1 從 **47.27 → 62.64（+15.37）**。
