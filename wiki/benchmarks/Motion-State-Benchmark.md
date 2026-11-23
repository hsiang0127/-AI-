---
type: benchmark
title: "Motion-State-Benchmark"
aliases: ["Motion State MOT Benchmark", "MVF1 Benchmark"]
domain: ["uav-tracking", "multi-object-tracking"]
tags: ["motion-state", "mvf1", "kitti", "mot17", "uavdt"]
sources: ["[[Liao2024-MoD2T]]"]
created: 2026-11-23
updated: 2026-11-23
---

# Motion-State-Benchmark（動靜狀態多目標追蹤基準與 MVF1）

## 定義與核心直覺
由 [[Liao2024-MoD2T]] 提出的新評測基準與指標 **`MVF1`（Motion State Validation F1）**，用來彌補傳統 MOT 基準（HOTA/MOTA/IDF1）無法衡量「追蹤器是否正確分辨目標相對地面為移動或靜止」的缺口。

## 演進脈絡與關鍵變體
- **資料集組成**：整合三大經典基準並補齊動靜真值——**KITTI**（由 3D 座標推導動靜，細分慢速與快速相機）、**MOT17**（行人，人工補標）、[[UAVDT]]（無人機車輛，人工補標）。
- **核心指標 `MVF1`**：
  $$MVF1 = 2\cdot\frac{MVP\cdot MVR}{MVP + MVR}, \quad MVR = \frac{MVTP}{MVTP + BF\cdot MVFN}$$
  其中平衡因子 $BF = \frac{1}{2}(\text{Precision} + \text{Recall})$ 動態調節漏偵測（$MVFN$）的懲罰權重。

## 優缺點與適用場景
- **優勢**：首次系統性量化「動靜判斷正確率」與「偵測完整度」的綜合表現，並明確揭露慢速相機（KITTI `0.775`、UAVDT `0.827`）與快速相機（KITTI `0.230`）之間的巨大效能斷層。
- **限制**：僅支援二元（移動／靜止）標籤，未評估「未確定狀態比例」、「啟停轉換確認延遲」與「重新啟動後 ID 保持率」。

## 跨論文觀點與矛盾
- 對應碩士研究規劃第十一節的實驗設計：在間隔航測影像中，除了動靜正確率外，還須額外報告**靜止誤報率、低速辨識率、未確定比例、容許區域涵蓋率與端到端發布延遲**。

## 相關頁面
- 相關概念：[[Motion-State-Estimation]]、[[Geolocation-Uncertainty]]
- 相關方法：[[MoD2T]]
- 相關基準：[[UAVDT]]
- 來源論文：[[Liao2024-MoD2T]]
