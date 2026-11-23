---
type: concept
title: "Motion State Estimation"
aliases: ["動靜狀態判斷", "啟停狀態推論", "Moving vs. Static"]
domain: ["uav-tracking", "computer-vision", "multi-object-tracking"]
tags: ["motion-state", "start-stop", "false-motion", "mvf1"]
sources: ["[[Liao2024-MoD2T]]", "[[Yang2024-SeaYouLater]]", "[[Yi2024-UCMCTrack]]"]
created: 2026-11-23
updated: 2026-11-23
---

# Motion State Estimation（動靜與啟停狀態判斷）

## 定義與核心直覺
在動態視角（尤其是無人機空拍）的多目標追蹤中，**動靜狀態判斷**旨在判別物件相對於地面究竟是「真實移動（Moving）」、「近似靜止（Stationary）」還是「證據不足（Uncertain）」，並追蹤其**停止與再啟動（Start-Stop）**的狀態轉換。

## 演進脈絡與關鍵變體
- **傳統背景建模（SGM / MOG2 / 光流）**：能直接抓出相對背景移動的前景像素，但缺乏精確物件邊界框，且在快速相機移動下崩潰（[[Liao2024-MoD2T]] Table VII）。
- **位移＋周圍背景雙線索融合（[[MoD2T]]）**：[[Liao2024-MoD2T]] 結合 ECC 補償後的馬氏位移距離 $A_m$、OSNet 計算的周圍背景 SSIM 相似度 $A_a$，以及傳統高斯背景減除遮罩，在 [[UAVDT]] 達成 `MVF1 0.827`，並提出 [[Motion-State-Benchmark]]。
- **不確定性感知的三態推論與啟停回饋（研究缺口）**：將「誤差範圍（[[Geolocation-Uncertainty]]）」與實務定義的「可忽略運動範圍」分開比較：
  - **支持移動**：位移不確定範圍與可忽略運動範圍明確分離。
  - **支持近似靜止**：位移不確定範圍足夠小且落於可忽略範圍內。
  - **未確定**：兩範圍重疊或資料品質不足（*未能證明移動 ≠ 已證明靜止*）。
  - 並透過多次觀測更新「疑似停止 → 確認近似靜止（保留 ID）→ 疑似再啟動 → 確認移動」，且將狀態回饋給跨照片關聯。

## 優缺點與適用場景
- **優勢**：大幅降低因定位漂移或背景視差造成的「靜止車輛假移動」誤報，並讓系統在目標暫停後重新起步時維持同一個 Track ID。
- **限制**：低速目標在短曝光間隔下的真實位移容易與定位雜訊重疊；兩次間隔曝光之間若目標移動後又回到原位，單靠淨位移無法察覺。

## 跨論文觀點與矛盾
- [[Liao2024-MoD2T]] 採二元分類（移動 vs. 靜止）且僅比較 $t$ 與 $t-3$ 幀，未將動靜結果回饋至追蹤器的 ID 關聯決策；在快速相機序列中 `MVF1` 跌至 `0.230`。這與間隔航測情境的需求形成對比——間隔航測不能靠連續 3 幀背景減除，而必須依靠經校準的地理位移誤差模型與多照片證據累積。

## 相關頁面
- 相關概念：[[Geolocation-Uncertainty]]、[[UAV-Multi-Object-Tracking]]、[[Camera-Motion-Compensation]]
- 相關方法：[[MoD2T]]
- 相關基準：[[Motion-State-Benchmark]]、[[UAVDT]]
- 跨論文綜述：[[UAV-Tracking-Geolocation-Synthesis]]
- 來源論文：[[Liao2024-MoD2T]]、[[Yang2024-SeaYouLater]]、[[Yi2024-UCMCTrack]]
