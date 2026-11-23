---
type: concept
title: "Geolocation Uncertainty"
aliases: ["定位不確定性", "誤差傳播", "位移不確定性", "Error Ellipse"]
domain: ["uav-tracking", "photogrammetry", "estimation"]
tags: ["uncertainty", "covariance-propagation", "mahalanobis-distance", "calibration"]
sources: ["[[Yi2024-UCMCTrack]]", "[[Yang2024-SeaYouLater]]", "[[Liao2024-MoD2T]]"]
created: 2026-11-23
updated: 2026-11-23
---

# Geolocation Uncertainty（定位不確定性與誤差傳播）

## 定義與核心直覺
當我們將 2D 航拍影像上的物件透過相機幾何、UAV 直接定位資訊（GPS/IMU）與數值地表模型（DSM）投射到地理／世界座標時，感測器噪聲、姿態角誤差、高程誤差與影像參考點偏移都會被透視幾何放大，使估計座標 $\hat{p}_i = p_i + e_i$ 帶有非等向性的定位誤差。

在跨照片追蹤中，必須嚴格區分：
1. **單次定位不確定性（$\Sigma_i$）**：單張照片估計的地理座標可能偏離真值多少。
2. **跨照片位移不確定性（$S_{ij}$）**：兩張照片座標差 $\Delta \hat{p}_{ij} = \Delta p_{ij} + (e_j - e_i)$ 中，有多少可能單純來自定位誤差：
   $$S_{ij} = \Sigma_i + \Sigma_j - C_{ij} - C_{ij}^\top$$
   其中 $C_{ij}$ 為兩次誤差的交叉協方差。若共同偏差部分抵消，真實位移不確定性會小於獨立假設；若存在方向性偏差，則會對靜止物件造成系統性的**假位移**。

## 演進脈絡與關鍵變體
- **忽略誤差的純歐氏距離**：[[Yang2024-SeaYouLater]]（[[MG-MOT]]）直接用 UAV 元資料投影至 3D 海面並以歐氏距離配對，但作者在 Fig. 3 親證元資料俯仰角異常與時間不同步會直接導致嚴重座標漂移。
- **單次投影協方差（誤差橢圓）**：[[Yi2024-UCMCTrack]]（[[UCMCTrack]]）透過單應性 Jacobian 將影像偵測雜訊傳播至地面（$\mathbf{R}_k = \mathbf{C}\mathbf{R}_k^{uv}\mathbf{C}^\top$），形成旋轉相關誤差橢圓（CMD）並以映射馬氏距離（MMD）關聯，大幅勝過純歐氏距離與 IoU；同時 Fig. 4c 證明相機俯仰／滾轉角誤差對地面變形極為敏感。
- **啟發式影像協方差**：[[Liao2024-MoD2T]] 僅以邊界框寬高建立對角矩陣計算馬氏距離，未納入真實地理定位與相機對齊不確定性。
- **經校準的適應性跨照片位移誤差模型（研究缺口）**：利用可信的靜止參考地物校準跨曝光假位移（分開處理偏差與離散度），再將不確定性直接轉化為 [[Motion-State-Estimation]]（移動／近似靜止／未確定）與近即時關聯證據。

## 優缺點與適用場景
- **優勢**：可量化每筆位移觀測的可信度，在相同靜止誤報率下提升低速移動與再啟動辨識能力，並避免將「證據不足」誤判為靜止或移動。
- **限制**：需要良好的幾何誤差模型與獨立靜止參考物校準；若位置受地表高度強約束，協方差矩陣可能退化，需在有效維度（水平橢圓或地表切平面）處理。

## 跨論文觀點與矛盾
- [[Yi2024-UCMCTrack]] 僅需傳播單次影像偵測誤差即可在連續影片取得高分，但假設平坦地面且跨幀誤差獨立；在間隔航測影像中，相機姿態、DSM 與物件參考點高度誤差常遠大於像素偵測誤差，且具有跨照片相關性（$C_{ij} \ne 0$），因此不能直接套用獨立誤差傳播。

## 相關頁面
- 相關概念：[[UAV-Multi-Object-Tracking]]、[[Motion-State-Estimation]]、[[Camera-Motion-Compensation]]
- 相關方法：[[UCMCTrack]]、[[MG-MOT]]
- 跨論文綜述：[[UAV-Tracking-Geolocation-Synthesis]]
- 來源論文：[[Yi2024-UCMCTrack]]、[[Yang2024-SeaYouLater]]、[[Liao2024-MoD2T]]
