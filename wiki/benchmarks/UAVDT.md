---
type: benchmark
title: "UAVDT"
aliases: ["UAV Detection and Tracking Benchmark"]
domain: ["uav-tracking", "multi-object-tracking"]
tags: ["uav-mot", "vehicle-tracking", "motion-state"]
sources: ["[[Ma2026-AMOT]]", "[[Liao2024-MoD2T]]"]
created: 2026-11-23
updated: 2026-11-23
---

# UAVDT（無人機車輛偵測與追蹤基準）

## 定義與核心直覺
UAVDT 是專注於無人機空拍交通車輛的偵測與多目標追蹤基準，涵蓋不同航高（低／中／高空）、天氣、光照（日間／夜間／起霧）與相機運動模式。

## 演進脈絡與關鍵變體
- **標準多目標追蹤**：[[Ma2026-AMOT]] 的 [[AMOT]] 在 UAVDT 測試集達成 **IDF1 74.7% / MOTA 55.1% / IDs 272**，超越 STCMOT（69.8% IDF1）與 DroneMOT（69.6% IDF1）。
- **動靜狀態擴充標註**：[[Liao2024-MoD2T]] 針對 UAVDT 子集（測試集 `M101`、`M202`、`M204`、`M402`、`M601`、`M703`、`M802`）人工補標車輛相對地面的移動／靜止狀態，納入 [[Motion-State-Benchmark]]，[[MoD2T]] 在此達成 `MVF1 0.827`。

## 優缺點與適用場景
- **優勢**：涵蓋多種航高與光照變化，且同時橫跨傳統追蹤與動靜狀態判斷兩類研究。
- **限制**：為連續高幀率影片且不含地理定位元資料與 DSM，與等距間隔航測影像（曝光間隔大、具地理參考）仍有場景落差。

## 跨論文觀點與矛盾
- 在 UAVDT 上相機運動相對平緩，使 [[MoD2T]] 的 2D ECC + 背景減除表現亮眼（`MVF1 0.827`），但這並不能直接推論到快速移動或大基線間隔航測場景（對比 KITTI Fast 驟降至 `0.230`）。

## 相關頁面
- 相關概念：[[UAV-Multi-Object-Tracking]]、[[Motion-State-Estimation]]
- 相關方法：[[AMOT]]、[[MoD2T]]
- 相關基準：[[VisDrone]]、[[Motion-State-Benchmark]]
- 來源論文：[[Ma2026-AMOT]]、[[Liao2024-MoD2T]]
