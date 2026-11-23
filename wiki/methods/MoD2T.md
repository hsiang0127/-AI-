---
type: method
title: "MoD2T"
aliases: ["Model-Data-Driven Motion State Tracking"]
domain: ["uav-tracking", "multi-object-tracking", "computer-vision"]
tags: ["motion-state", "background-subtraction", "ecc", "osnet", "mvf1"]
sources: ["[[Liao2024-MoD2T]]"]
created: 2026-11-23
updated: 2026-11-23
---

# MoD2T（模型與資料雙驅動動靜狀態追蹤框架）

## 定義與核心直覺
MoD2T 由 [[Liao2024-MoD2T]] 提出，透過結合「深度學習追蹤器的位移與背景相似度判斷」與「傳統雙模態高斯背景減除模型」，在單目 RGB 影像下判斷每個追蹤目標相對於地面為移動或靜止。

## 演進脈絡與關鍵變體
- **深度分支**：以 YOLOv8 + StrongSORT / DeepOCSORT 追蹤後，對間隔 $n=3$ 幀的目標計算 ECC 補償後馬氏位移分數 $A_m$ 與 OSNet 周圍背景 SSIM 相似度 $A_a$，加權得 $\Theta = \lambda A_a + (1-\lambda) A_m$。
- **傳統分支**：以改良雙模態單高斯背景模型（SGM）+ OC-SORT 取得運動前景像素遮罩。
- **失效自動判別與融合**：檢查前景像素占比與單框內連通元件數（$n \ge 2$ 判定目標沾黏失效），動態切換「框＋分數融合」或「僅深度框＋前景比例輔助融合」。

## 優缺點與適用場景
- **優勢**：在慢速／靜止相機場景（如 [[UAVDT]]）大幅拉高動靜辨識率（`MVF1 0.827`，+30.2%），且不損及原追蹤器 HOTA/IDF1。
- **限制**：在快速相機場景（KITTI Fast）因 2D ECC 無法補償 3D 視差而崩潰（`MVF1 0.230`）；缺乏機率不確定性傳播、「未確定」狀態與啟停回饋關聯機制。

## 跨論文觀點與矛盾
- 證明單靠 2D 影像背景建模與 ECC 無法應對大視角變換的動靜判斷，間接佐證航測追蹤須結合地理座標與 [[Geolocation-Uncertainty]]。

## 相關頁面
- 相關概念：[[Motion-State-Estimation]]、[[Camera-Motion-Compensation]]
- 評測基準：[[Motion-State-Benchmark]]、[[UAVDT]]
- 來源論文：[[Liao2024-MoD2T]]
