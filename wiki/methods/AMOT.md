---
type: method
title: "AMOT"
aliases: ["Appearance-Guided Motion Modeling Tracker"]
domain: ["uav-tracking", "multi-object-tracking", "computer-vision"]
tags: ["amc", "mtc", "dense-response-map", "jde", "track-continuation"]
sources: ["[[Ma2026-AMOT]]"]
created: 2026-11-23
updated: 2026-11-23
---

# AMOT（外觀引導運動建模無人機追蹤器）

## 定義與核心直覺
AMOT 由 [[Ma2026-AMOT]] 提出，針對無人機不穩定視角與短暫漏偵測問題，在 JDE（DLA-34）密集 ReID 特徵圖上設計兩個免訓練、即插即用模組，讓外觀特徵直接引導空間運動對應與未匹配軌跡延續。

## 演進脈絡與關鍵變體
- **AMC（Appearance-Motion Consistency）**：同時計算「軌跡 ReID → 當前幀特徵圖」的前向峰值距離 $\mathbf{D}_f$，與「偵測 ReID → 前一幀特徵圖」的後向峰值距離 $\mathbf{D}_b$，經高斯核（$\sigma=5$）轉為一致性代價 $\mathbf{C}_{AMC}$，與 $\mathbf{C}_{IOU}$、$\mathbf{C}_{App}$ 統一融合。
- **MTC（Motion-aware Track Continuation）**：針對兩階段關聯後仍漏匹配的軌跡，比對 Kalman Filter 預測中心與密集回應圖外觀峰值中心；若距離 $d_k < 3$ 且無重疊偵測，則主動延續軌跡。
- **即插即用泛化**：可直接掛載至 UAVMOT、STCMOT 等其他 JDE 追蹤器，在 [[VisDrone]] 上分別提升 +4.4% 與 +7.0% IDF1。

## 優缺點與適用場景
- **優勢**：免訓練、額外耗時極低（36.4 FPS，僅降 0.7 FPS）；在 [[VisDrone]]（61.4% IDF1）與 [[UAVDT]]（74.7% IDF1）皆達 SOTA，ID 切換砍掉超過 70%。
- **限制**：完全運作在 2D 影像平面，無法區分相機視差與真實移動；全域 `arg max` 與 20 幀連續 FIFO 緩衝在稀疏間隔航測影像下容易誤鎖干擾物或無法觸發。

## 跨論文觀點與矛盾
- 與 [[UCMCTrack]]（純地面幾何）和 [[LightGlue]]（稀疏局部關鍵點幾何）形成對比：AMOT 仰賴單一物件級 ReID 嵌入與密集回應圖，對連續空拍影片極有效，但在大基線間隔航測中缺乏 3D 幾何約束。

## 相關頁面
- 相關概念：[[UAV-Multi-Object-Tracking]]、[[Camera-Motion-Compensation]]
- 評測基準：[[VisDrone]]、[[UAVDT]]
- 來源論文：[[Ma2026-AMOT]]
