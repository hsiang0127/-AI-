# LLM Wiki Index

> 本頁為論文知識庫的內容導向總目錄，由 Agent 於每次 Ingest / Query 沉澱 / Lint 後同步維護。

## 領域總覽（Domains）
- `uav-tracking` / `multi-object-tracking` — 無人機多目標追蹤、跨視角關聯、動靜狀態推論與長時程重辨識
- `photogrammetry` / `estimation` — 航測地理定位、定位不確定性、誤差傳播與跨照片位移校準
- `computer-vision` / `feature-matching` — 相機運動補償、局部特徵匹配與剛體幾何對應

---

## Papers（單篇論文筆記）
- [[Yang2024-SeaYouLater]] — 利用 UAV 元資料將偵測投影至 3D 海面世界座標並以 BiME/MTE 完成長時程 Re-ID，奪 2024 MaCVi SeaDronesSee 第一名（`uav-tracking`, `multi-object-tracking`）
- [[Yi2024-UCMCTrack]] — 捨棄影像 IoU 與逐幀 CMC，以地面投影旋轉誤差橢圓（CMD）、映射馬氏距離（MMD）與統一過程雜訊達 >1000 FPS 純運動追蹤（`uav-tracking`, `multi-object-tracking`）
- [[Liao2024-MoD2T]] — 結合深度位移／周圍背景相似度與傳統高斯背景減除判斷目標相對地面動靜，提出 `MVF1` 指標與動靜基準（`uav-tracking`, `multi-object-tracking`）
- [[Ma2026-AMOT]] — 提出免訓練的外觀－運動雙向一致性矩陣（AMC）與漏偵測軌跡延續（MTC），大幅降低無人機空拍 ID 切換並維持 36.4 FPS（`uav-tracking`, `multi-object-tracking`）
- [[Lindenberger2023-LightGlue]] — 透過 RoPE、雙向交叉注意力與自適應深度提早停止／點剪枝，將深度局部特徵匹配加速 2×～4×（`computer-vision`, `feature-matching`）

## Concepts（核心概念與理論）
- [[UAV-Multi-Object-Tracking]] — 無人機動態視角下的多目標追蹤任務定義、技術演進與外觀 vs. 幾何適用邊界（`uav-tracking`, `multi-object-tracking`）
- [[Geolocation-Uncertainty]] — 單次定位誤差與跨照片位移誤差傳播（$S_{ij} = \Sigma_i + \Sigma_j - C_{ij} - C_{ij}^\top$）、誤差橢圓與假位移校準（`uav-tracking`, `photogrammetry`, `estimation`）
- [[Camera-Motion-Compensation]] — 逐幀 2D 影像配準（ECC/光流）、統一過程雜訊補償與 3D 地理投影補償的優劣與實證衝突（`uav-tracking`, `computer-vision`）
- [[Motion-State-Estimation]] — 相對地面移動／靜止／未確定三態判斷、啟停狀態轉換與回饋追蹤關聯（`uav-tracking`, `multi-object-tracking`）
- [[Local-Feature-Matching]] — 保留關鍵點空間幾何分布的跨視角特徵對應（SIFT、SuperPoint + LightGlue）與航測剛體匹配（`computer-vision`, `feature-matching`）

## Methods（方法、演算法與模型）
- [[MG-MOT]] — UAV 元資料引導的 3D 世界座標短／長時程重辨識追蹤方法（`uav-tracking`, `multi-object-tracking`）
- [[UCMCTrack]] — 地面座標 Kalman Filter 結合 CMD 投影協方差與 MMD 馬氏距離的純運動追蹤器（`uav-tracking`, `multi-object-tracking`）
- [[MoD2T]] — 深度追蹤器與雙模態高斯背景減除自適應融合的動靜判斷框架（`uav-tracking`, `multi-object-tracking`）
- [[AMOT]] — 基於 JDE 密集 ReID 回應圖的免訓練外觀引導運動建模與軌跡延續方法（`uav-tracking`, `multi-object-tracking`）
- [[LightGlue]] — 具備自適應深度停止與點剪枝的高速深度局部特徵匹配器（`computer-vision`, `feature-matching`）

## Benchmarks（評測基準與資料集）
- [[SeaDronesSee]] — 附帶逐幀 GPS/航高/雲台元資料的海面無人機長時程 Re-ID 追蹤基準（`uav-tracking`）
- [[VisDrone]] — 涵蓋密集小目標與多變視角的城市空拍無人機多目標追蹤基準（`uav-tracking`）
- [[UAVDT]] — 涵蓋多航高與光照變化的無人機交通車輛追蹤基準（亦含動靜擴充標註）（`uav-tracking`）
- [[Motion-State-Benchmark]] — 結合 KITTI/MOT17/UAVDT 與 `MVF1` 指標的相對地面動靜狀態評測基準（`uav-tracking`, `multi-object-tracking`）

## Syntheses（跨論文比較與主題綜述）
- [[UAV-Tracking-Geolocation-Synthesis]] — 跨 5 篇論文的完整技術比較表、研究缺口對照與零基礎教學簡報故事線藍圖（`uav-tracking`, `photogrammetry`, `multi-object-tracking`）
- [[Presentation-Script-UAV-Tracking]] — 14 頁日式文青風教學簡報完整逐頁腳本、白話類比與 Wiki 來源對照（`uav-tracking`, `teaching-presentation`）
