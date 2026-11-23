---
type: benchmark
title: "VisDrone"
aliases: ["VisDrone2019-MOT", "VisDrone-MOT"]
domain: ["uav-tracking", "multi-object-tracking"]
tags: ["uav-mot", "small-object", "crowded-scene"]
sources: ["[[Ma2026-AMOT]]"]
created: 2026-11-23
updated: 2026-11-23
---

# VisDrone（無人機空拍多目標追蹤基準）

## 定義與核心直覺
VisDrone（特別是 VisDrone2019-MOT）是電腦視覺領域最廣泛使用的無人機空拍多目標追蹤基準之一，涵蓋城市道路、行人、車輛等密集小目標與多變無人機俯仰／平移視角。

## 演進脈絡與關鍵變體
- 主要評測指標為 IDF1、MOTA、MT、ML、IDs 與 FPS。
- 在 [[Ma2026-AMOT]] 中，[[AMOT]] 於 VisDrone2019 測試集達成 **IDF1 61.4% / MOTA 46.0% / IDs 1063（36.4 FPS）**，大幅超越 ByteTrack（IDF1 37.0%）與 DroneMOT（IDF1 58.6%），並透過抽幀（間隔 1～10 幀）測試大位移下的關聯穩健性。

## 優缺點與適用場景
- **優勢**：目標種類多、密集遮擋與無人機視角變化豐富，非常適合驗證 2D 外觀與運動關聯演算法。
- **限制**：未提供 UAV GPS/IMU 直接定位元資料、相機內外方位與 DSM，也未標註目標相對地面的動靜狀態，無法直接評估地理定位誤差模型。

## 跨論文觀點與矛盾
- [[Ma2026-AMOT]] 在 VisDrone 上主要報告 IDF1/MOTA 而未報告 HOTA，跨論文比較時須留意評測指標差異。

## 相關頁面
- 相關概念：[[UAV-Multi-Object-Tracking]]
- 相關方法：[[AMOT]]
- 相關基準：[[UAVDT]]
- 來源論文：[[Ma2026-AMOT]]
