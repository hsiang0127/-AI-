---
type: benchmark
title: "SeaDronesSee"
aliases: ["SeaDronesSee-MOT", "MaCVi SeaDronesSee"]
domain: ["uav-tracking", "multi-object-tracking"]
tags: ["maritime", "uav-metadata", "long-term-reid", "small-object"]
sources: ["[[Yang2024-SeaYouLater]]"]
created: 2026-11-23
updated: 2026-11-23
---

# SeaDronesSee（海面無人機多目標追蹤與重辨識基準）

## 定義與核心直覺
SeaDronesSee-MOT（MaCVi 競賽基準）是專為**海面無人機搜救多目標追蹤與長時程重辨識（MOT with Re-ID）**設計的資料集，最大特色是**逐幀附帶完整的 UAV 感測器元資料**（GPS 經緯度、航高 `4.6～149.5m`、雲台俯仰角 `gimbal_pitch`、航向角 `gimbal_heading` 及三軸速度）。

## 演進脈絡與關鍵變體
- 測試集包含 19 段影片（18,253 幀，總計全資料集逾 54,000 幀、約 40 萬筆標註），要求追蹤器在泳客與船隻離開視野再重現時恢復原有 ID。
- 訓練與驗證集本身不含長時程 Re-ID 標註，因此長時程重辨識能力主要在測試集上透過 HOTA 與 IDF1 驗證。
- [[Yang2024-SeaYouLater]] 的 [[MG-MOT]] 於 2024 MaCVi 競賽取得 **HOTA 69.5% / IDF1 85.9% / IDs 18** 第一名（官方基線 HOTA 37.5%）。

## 優缺點與適用場景
- **優勢**：少數提供真實 UAV 定位與姿態元資料的公開 MOT 基準，且包含真實感測器元資料異常與不同步案例（見 [[Yang2024-SeaYouLater]] Fig. 3）。
- **限制**：場景為平坦海面（$z=0$），不包含陸地地形高程變化（DSM）與密集啟停交通情境。

## 跨論文觀點與矛盾
- 測試集中存在元資料錯誤（如 `gimbal_pitch` 誤差達 $45^\circ$），直接凸顯未建模 [[Geolocation-Uncertainty]] 的風險。

## 相關頁面
- 相關概念：[[UAV-Multi-Object-Tracking]]、[[Geolocation-Uncertainty]]
- 相關方法：[[MG-MOT]]
- 來源論文：[[Yang2024-SeaYouLater]]
