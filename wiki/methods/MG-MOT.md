---
type: method
title: "MG-MOT"
aliases: ["Metadata-Guided MOT", "Sea You Later"]
domain: ["uav-tracking", "multi-object-tracking"]
tags: ["uav-metadata", "world-coordinate", "long-term-reid", "bime", "mte"]
sources: ["[[Yang2024-SeaYouLater]]"]
created: 2026-11-23
updated: 2026-11-23
---

# MG-MOT（元資料引導多目標追蹤）

## 定義與核心直覺
MG-MOT 是由 [[Yang2024-SeaYouLater]] 提出的無人機多目標追蹤方法，核心直覺是「當海面小目標外觀幾乎一模一樣時，改用 UAV 逐幀感測器元資料（GPS、航高、雲台姿態）將目標投影到 3D 世界座標，在真實物理空間進行短／長時程重辨識」。

## 演進脈絡與關鍵變體
- 前端基於 `YOLOv8x-p2`（含小目標頭並與 COCO `YOLOv8x` 整合）+ `BoT-SORT`（含稀疏光流 [[Camera-Motion-Compensation|GMC]]）。
- **核心模組**：
  1. **Estimated Camera Model**：校準 $fov = 70^\circ$，以雲台 pitch/yaw 建構旋轉矩陣並將 2D 框中心射線與海平面（$z=0$）求交得 3D 世界座標。
  2. **Short-Term Re-ID**：於 3D 世界座標計算歐氏距離執行匈牙利配對，修補鏡頭急轉斷軌。
  3. **Long-Term Re-ID（BiME + MTE）**：結合雙向速度外插（Bi-Directional Movement Extrapolation）與隨消失時間線性擴張的匹配門檻（Matching Threshold Expansion），在 [[SeaDronesSee]] 達成 HOTA **69.5%**、IDF1 **85.9%**。

## 優缺點與適用場景
- **優勢**：無須外觀 Re-ID 即可在劇烈視角切換與目標進出視野後維持高 IDF1（+17.2%）；計算開銷低。
- **限制**：假設平坦海面（$z=0$）與等速外插；對元資料誤差或時間不同步極為敏感（未建構 [[Geolocation-Uncertainty]] 模型），且無法區分定位漂移與真實低速移動。

## 跨論文觀點與矛盾
- 相較於 [[UCMCTrack]] 透過 Jacobian 計算地面投影誤差橢圓並使用馬氏距離，MG-MOT 直接使用歐氏距離與線性擴張門檻，因此在感測器姿態偏移時抗雜訊能力較弱。

## 相關頁面
- 相關概念：[[UAV-Multi-Object-Tracking]]、[[Geolocation-Uncertainty]]、[[Camera-Motion-Compensation]]
- 評測基準：[[SeaDronesSee]]
- 來源論文：[[Yang2024-SeaYouLater]]
