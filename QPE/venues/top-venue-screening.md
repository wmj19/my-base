# 顶会顶刊筛选标准

最后更新：2026-05-28

## 本轮纳入标准

核心论文必须同时满足：

1. 任务是 QPE、precipitation estimation、precipitation retrieval、satellite precipitation fusion、bias correction 或 downscaling，输出是当前/历史降水强度或累计量。
2. 方法明确包含深度学习或神经网络。
3. 输入包含卫星观测或卫星降水产品。
4. 来源是顶刊、强领域期刊、高质量会议或 arXiv 前沿预印本。

## 顶刊/强领域期刊口径

本专题优先关注以下类别：

- 遥感顶刊：Remote Sensing of Environment、IEEE Transactions on Geoscience and Remote Sensing、ISPRS Journal of Photogrammetry and Remote Sensing。
- 气象/大气顶刊：Journal of Geophysical Research: Atmospheres、Geophysical Research Letters、Journal of Hydrometeorology、Journal of Climate、Bulletin of the American Meteorological Society、npj Climate and Atmospheric Science。
- 水文顶刊/强刊：Journal of Hydrology、Water Resources Research、Hydrology and Earth System Sciences、Atmospheric Research。
- AI 顶会：CVPR、ICCV、ICLR、ICML、NeurIPS、AAAI。只有严格 QPE 才纳入核心；仅 nowcasting/QPF 的论文放入相邻方向。
- 预印本：arXiv 论文如果来自强团队、方法前沿、数据和任务符合 QPE，则纳入“前沿候选”，但需要标注未同行评审。

## 排除或降级处理

- 只评估卫星产品、不提出深度学习 QPE 方法：作为背景，不列核心。
- 纯 QPF/nowcasting：作为相邻方向，不列核心。
- 只使用雷达、雨量站或 NWP，不含卫星输入：不纳入本轮核心。
- 仅普通机器学习且无深度学习：除非对数据集或评估口径特别重要，否则不列核心。
- 低质量、信息不完整、无法核验来源的论文：放入 backlog 待查。

## 本轮顶会检索结论

近三年 CVPR/ICLR/ICML/AAAI/NeurIPS 中，严格满足“卫星输入 + 深度学习 + QPE”的论文很少。检索到的高相关顶会论文多偏未来降水预报，例如 AAAI 2025 的 `Data-driven Precipitation Nowcasting Using Satellite Imagery`。这类论文对模型设计和卫星图像表征有参考价值，但不应替代 QPE 文献主线。

