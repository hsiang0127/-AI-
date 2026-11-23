# 考量定位不確定性之無人機間隔航測影像近即時移動物件追蹤與運動估計
## Near-Real-Time Moving Object Tracking and Motion Estimation from Interval UAV Photogrammetric Imagery Accounting for Geolocation Uncertainty

> 來源：使用者提供之碩士研究規劃（作為本 LLM Wiki 主題定調與研究缺口對照之核心參考文件）

### 核心研究問題
在間隔航測影像中，如何利用經校準的定位不確定性，區分假位移與真實移動，並讓這項證據改善近即時追蹤與運動估計？

### 三大擬議貢獻
1. **經校準的適應性位移誤差模型與運動狀態推論**：考量觀測幾何、偏差與跨照片誤差相關性（$S_{ij} = \Sigma_i + \Sigma_j - C_{ij} - C_{ij}^T$），區分支持移動、支持近似靜止與未確定狀態。
2. **定位不確定性感知的近即時追蹤決策**：結合外觀與局部特徵（SIFT / SuperPoint + LightGlue）、位移可信度、啟停狀態與觀測機會，執行跨照片關聯與有限延遲確認。
3. **航測條件下的可靠度與時效性分析**：量化定位品質、影像尺寸、曝光間隔與發布延遲對追蹤與運動估計的影響。

### 關鍵對照文獻
- [1] Yang et al. (2024). Sea You Later (MG-MOT). WACV Workshops. `arXiv:2311.03561`
- [2] Yi et al. (2024). UCMCTrack. AAAI. `arXiv:2312.08952`
- [3] Liao / Feng et al. (2024). Motion State (MoD2T). `arXiv:2312.17641`
- [4] WM-SORT (2025). Neurocomputing.
- [5] Claasen & de Villiers (2026). IMM-JHSE. Expert Systems with Applications.
- [6] Ma et al. (2026). Tracking the Unstable (AMOT). AAAI. `arXiv:2508.01730`
- [7] ADAPT-Tracker (2026). ISPRS JPRS.
- [8] STC-SORT (2026). Applied Sciences.
- [9] Wang et al. (2026). Time-Adaptive Data Association. Sensors.
