---
type: concept
title: "UAV Multi-Object Tracking"
aliases: ["無人機多目標追蹤", "UAV-MOT", "Aerial MOT"]
domain: ["uav-tracking", "computer-vision", "multi-object-tracking"]
tags: ["uav", "mot", "photogrammetry", "sparse-observation"]
sources: ["[[Yang2024-SeaYouLater]]", "[[Yi2024-UCMCTrack]]", "[[Liao2024-MoD2T]]", "[[Ma2026-AMOT]]"]
created: 2026-11-23
updated: 2026-11-23
---

# UAV Multi-Object Tracking（無人機多目標追蹤）

## 定義與核心直覺
無人機多目標追蹤（UAV-MOT）是指在無人機空拍影像序列中，持續偵測多個地面或海面目標並跨幀維持其唯一身份（Track ID）、估計軌跡與運動狀態的任務。與固定監視器不同，無人機本身處於 6 自由度動態移動中，使影像平面上的像素位移同時混雜了「相機自身運動」與「目標真實運動」。

## 演進脈絡與關鍵變體
- **2D 影像平面追蹤 + 外觀引導**：如 [[Ma2026-AMOT]] 在 JDE 架構上透過前／後向外觀回應圖建立空間一致性（[[AMOT]]），解決連續空拍影片中的劇烈晃動與漏偵測。
- **地面／世界座標投影追蹤**：如 [[Yi2024-UCMCTrack]] 透過單應性將偵測投射至地面平面並計算投影協方差（[[UCMCTrack]]）；[[Yang2024-SeaYouLater]] 進一步利用 UAV 的 GPS、航高與雲台姿態直接投影至 3D 世界座標（[[MG-MOT]]），突破小目標外觀無辨識度的長時程 Re-ID 瓶頸。
- **相對地面動靜狀態追蹤**：如 [[Liao2024-MoD2T]] 指出傳統 MOT 未區分移動與靜止目標，提出 [[MoD2T]] 與 `MVF1` 指標。
- **間隔航測影像追蹤（本研究方向）**：測繪任務採等距間隔拍攝（時間不連續、視角跳變大、但具備直接定位資訊與 DSM），需結合 [[Geolocation-Uncertainty]] 與 [[Local-Feature-Matching]] 才能在有限延遲內可靠追蹤。

## 優缺點與適用場景
- **核心挑戰**：目標像素尺寸極小、外觀高度相似、大視角變換造成 `IoU = 0`、相機姿態／GPS 元資料誤差導致靜止物件產生假位移、以及實務發布時限（近即時性）。
- **適用場景**：智慧交通監測、海面搜救（[[SeaDronesSee]]）、空拍測繪同步動態監控（[[VisDrone]]、[[UAVDT]]）。

## 跨論文觀點與矛盾
- **外觀特徵到底有沒有用？**
  - [[Ma2026-AMOT]] 在 [[VisDrone]] / [[UAVDT]] 證明密集外觀回應圖能大幅減少 ID 切換（-72.4%）。
  - ⚠️ 衝突／適用邊界：[[Yang2024-SeaYouLater]] 在海面遠距空拍與 [[Yi2024-UCMCTrack]] 在相似外觀群體（DanceTrack）指出，當目標極小或外觀趨同時，外觀 Re-ID 反而不可靠，必須仰賴幾何與世界座標運動建模；在間隔航測中則需結合 [[Local-Feature-Matching]] 保留空間剛體配置而非單一外觀向量。

## 相關頁面
- 相關概念：[[Geolocation-Uncertainty]]、[[Camera-Motion-Compensation]]、[[Motion-State-Estimation]]、[[Local-Feature-Matching]]
- 跨論文綜述：[[UAV-Tracking-Geolocation-Synthesis]]
- 來源論文：[[Yang2024-SeaYouLater]]、[[Yi2024-UCMCTrack]]、[[Liao2024-MoD2T]]、[[Ma2026-AMOT]]
