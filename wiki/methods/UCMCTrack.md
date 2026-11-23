---
type: method
title: "UCMCTrack"
aliases: ["Uniform Camera Motion Compensation Tracker"]
domain: ["uav-tracking", "multi-object-tracking", "computer-vision"]
tags: ["ground-plane", "kalman-filter", "mahalanobis-distance", "covariance-propagation", "cmc"]
sources: ["[[Yi2024-UCMCTrack]]"]
created: 2026-11-23
updated: 2026-11-23
---

# UCMCTrack（統一相機運動補償地面座標追蹤器）

## 定義與核心直覺
UCMCTrack 是由 [[Yi2024-UCMCTrack]] 提出的純運動多目標追蹤器，捨棄 2D 影像平面 IoU 與耗時的逐幀影像配準，改將偵測投影至地面座標並透過 Jacobian 誤差傳播建立「旋轉誤差橢圓」，再以映射馬氏距離（MMD）完成關聯。

## 演進脈絡與關鍵變體
- **三大核心組件**：
  1. **Correlated Measurement Distribution (CMD)**：透過單應性反矩陣 Jacobian $\mathbf{C}$ 將偵測雜訊 $\mathbf{R}_k^{uv}$ 傳播為非對角地面協方差 $\mathbf{R}_k = \mathbf{C}\mathbf{R}_k^{uv}\mathbf{C}^\top$。
  2. **Process Noise Compensation (PNC / UCMC)**：整個序列使用單組固定過程雜訊 $(\sigma_x, \sigma_y)$ 吸收相機運動加速度，免去逐幀 CMC。
  3. **Mapped Mahalanobis Distance (MMD)**：$D = \boldsymbol{\epsilon}^\top \mathbf{S}^{-1}\boldsymbol{\epsilon} + \ln|\mathbf{S}|$，同時衡量位置殘差與量測不確定性。
- **變體 `UCMCTrack+`**：額外疊加逐幀 ECC 補償，在 MOT17 進一步提升（65.8 HOTA），但在低幀率劇烈晃動的 KITTI 上反而倒退（77.1 → 74.2）。

## 優缺點與適用場景
- **優勢**：純運動不需外觀模型，單核 CPU 超過 **1000 FPS**；在 `IoU = 0` 的大位移與相似外觀群體（DanceTrack）上表現卓越。
- **限制**：假設單一平坦地面（$z = z_0$）；高度仰賴相機內外方位準確度（X/Z 軸角度誤差會造成地面嚴重變形）；未建模跨曝光誤差交叉協方差 $C_{ij}$。

## 跨論文觀點與矛盾
- 與 [[AMOT]] 代表「純 3D 幾何運動」vs.「純 2D 外觀引導」兩種極端設計哲學；在間隔航測中，UCMCTrack 的誤差橢圓思想是 [[Geolocation-Uncertainty]] 的關鍵基石，但須以 DSM 取代平坦地面並校準跨曝光相關誤差。

## 相關頁面
- 相關概念：[[Geolocation-Uncertainty]]、[[Camera-Motion-Compensation]]、[[UAV-Multi-Object-Tracking]]
- 跨論文綜述：[[UAV-Tracking-Geolocation-Synthesis]]
- 來源論文：[[Yi2024-UCMCTrack]]
