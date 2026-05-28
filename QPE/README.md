# QPE

本目录用于整理“定量降水估计（Quantitative Precipitation Estimation, QPE）中利用深度学习方法、且输入数据包含卫星观测或卫星降水产品的研究”。

当前调研范围：

- 时间：近三年为主，默认按 `2023-05-28` 至 `2026-05-28` 理解；少数紧邻时间窗且高度相关的论文会单独标注。
- 任务：当前或历史降水估计、卫星降水反演、卫星降水产品偏差校正、卫星-雨量站/再分析/雷达融合、降水产品下采样/高分辨率重建。
- 方法：CNN、U-Net、ConvLSTM、Transformer、多任务学习、概率/分位数回归、扩散/生成式模型等深度学习方法。
- 输入：必须包含卫星数据，例如 GEO IR/VIS/WV、PMW、GPM、GOES、Meteosat、FY-4、IMERG、TRMM、PERSIANN、CMORPH、GSMaP、CHIRPS 等。
- 来源：优先顶刊和高质量预印本；顶会论文如果严格是 QPE 则纳入核心，如果只是降水预报/nowcasting 则放入相邻方向。

## 目录说明

- [index.md](index.md)：专题入口和当前结论。
- [literature/paper-index.json](literature/paper-index.json)：结构化论文索引。
- [surveys/2026-05-28_satellite-dl-qpe-top-venues-survey.md](surveys/2026-05-28_satellite-dl-qpe-top-venues-survey.md)：本轮深度调研综述。
- [datasets/satellite-qpe-datasets.md](datasets/satellite-qpe-datasets.md)：卫星 QPE 常用数据源与标签。
- [venues/top-venue-screening.md](venues/top-venue-screening.md)：顶会顶刊筛选口径和边界说明。
- [backlog.md](backlog.md)：后续待查问题和更新计划。

## 当前结论

近三年严格意义的“卫星输入 + 深度学习 + QPE”核心论文主要集中在遥感、气象和水文顶刊，例如 Remote Sensing of Environment、IEEE TGRS、JGR Atmospheres、Journal of Hydrometeorology、Journal of Hydrology、Atmospheric Research、Geophysical Research Letters 等。CVPR、ICLR、ICML、AAAI 这类 AI 顶会近三年更常见的是 precipitation nowcasting / QPF，而不是当前时刻的卫星 QPE；这些论文可作为方法参考，但不应直接混入 QPE 核心清单。

