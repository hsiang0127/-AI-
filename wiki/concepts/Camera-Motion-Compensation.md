---
type: concept
title: "Camera Motion Compensation"
aliases: ["相機運動補償", "CMC", "GMC", "Global Motion Compensation"]
domain: ["uav-tracking", "computer-vision", "multi-object-tracking"]
tags: ["cmc", "ecc", "optical-flow", "homography", "uav"]
sources: ["[[Yi2024-UCMCTrack]]", "[[Liao2024-MoD2T]]", "[[Yang2024-SeaYouLater]]", "[[Ma2026-AMOT]]"]
created: 2026-11-23
updated: 2026-11-23
---

# Camera Motion Compensation（相機運動補償，CMC）

## 定義與核心直覺
相機運動補償（CMC / GMC）是指在移動相機（尤其是無人機與車載鏡頭）的影像序列中，估計相鄰兩幀之間的背景幾何變換（仿射或單應性矩陣），以扣除相機自身平移、旋轉與縮放所造成的背景像素偏移，使追蹤器能還原目標的真實位移。

## 演進脈絡與關鍵變體
- **傳統逐幀影像配準（Frame-by-Frame CMC）**：透過 ECC 最大化（如 StrongSORT、[[Liao2024-MoD2T]]）、ORB 特徵匹配或稀疏光流 + RANSAC（如 BoT-SORT、[[Yang2024-SeaYouLater]] 的短期追蹤基底）逐幀估計仿射變換。
- **統一過程雜訊補償（Uniform CMC）**：[[Yi2024-UCMCTrack]]（[[UCMCTrack]]）提出捨棄逐幀影像配準，改在地面座標 Kalman Filter 中以全序列固定過程雜訊 $(\sigma_x, \sigma_y)$ 吸收相機晃動，速度突破 1000 FPS。
- **無 CMC 的外觀雙向引導**：[[Ma2026-AMOT]]（[[AMOT]]）不執行顯式 CMC，改以密集 ReID 回應圖的全域峰值搜尋自動吸收無人機視角平移。
- **直接定位與 3D 幾何投影**：[[Yang2024-SeaYouLater]]（[[MG-MOT]]）與航測追蹤直接利用 UAV GPS/IMU 與 DSM 投射至世界座標，本質上是利用物理感測器做 3D 全域補償。

## 優缺點與適用場景
- **逐幀 2D CMC 的優勢**：不需額外感測器，在相機緩慢運動、場景近似平面時效果良好（如 MOT17 上 `UCMCTrack+` 比 `UCMCTrack` 提升 +1.5 HOTA）。
- **逐幀 2D CMC 的限制**：
  1. 高解析度下計算耗時。
  2. **3D 視差與大基線失效**：在低幀率或快速移動場景（如 KITTI 10 FPS）下，2D 單應性無法補償 3D 視差，參數估計不準反而大幅降低效能。

## 跨論文觀點與矛盾
- ⚠️ **實證衝突（CMC 究竟加分還是扣分？）**：
  - 在 MOT17 上，[[Yi2024-UCMCTrack]] 加入逐幀 ECC（`UCMCTrack+`）將 HOTA 由 `64.3` 推升至 `65.8`。
  - 但在 **KITTI（10 FPS 劇烈相機運動）** 上，`UCMCTrack+` 反而比不加 CMC 的 `UCMCTrack` 倒退（Car HOTA `77.1 → 74.2`）；同樣在 [[Liao2024-MoD2T]] 的 KITTI 快速相機序列中，依賴 ECC 的動靜判斷 MVF1 從 `0.775` 暴跌至 `0.230`。
  - **結論**：當時間間隔拉大或 3D 視差顯著時（如間隔航測影像），單靠 2D CMC 不可靠，必須結合 3D 地理幾何與 [[Geolocation-Uncertainty]]。

## 相關頁面
- 相關概念：[[UAV-Multi-Object-Tracking]]、[[Geolocation-Uncertainty]]、[[Local-Feature-Matching]]
- 相關方法：[[UCMCTrack]]、[[MoD2T]]、[[AMOT]]
- 來源論文：[[Yi2024-UCMCTrack]]、[[Liao2024-MoD2T]]、[[Yang2024-SeaYouLater]]、[[Ma2026-AMOT]]
