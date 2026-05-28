# 近三年卫星输入深度学习 QPE 顶会顶刊调研

调研日期：2026-05-28  
调研窗口：以 2023-05-28 至 2026-05-28 为主；个别 2023 年 4 月的高相关顶刊论文作为 near-boundary 背景保留。  
主题：利用深度学习方法实现 QPE，输入必须包含卫星观测或卫星降水产品。  
结论级别：已联网核验论文题名、来源、年份、DOI/URL 和任务方向；期刊分区、引用量、代码状态仍需后续系统更新。

## 1. 执行摘要

本轮检索显示，近三年严格意义的“卫星输入 + 深度学习 + QPE”主线，主要不在 CVPR/ICLR/ICML 这些通用 AI 顶会，而在遥感、气象、水文强刊中展开。顶会中有卫星降水方向的论文，但更多聚焦 precipitation nowcasting / QPF，即未来降水预报，而不是当前或历史降水估计。

核心研究可以分成四条路线：

1. **直接卫星反演**：从 GEO IR/VIS/WV、PMW、GLM 等观测直接估计降水强度，例如 GOES ABI+GLM、Meteosat IR、PERSIANN-U-Net、Oya、Huayu。
2. **多任务分类-回归**：把 rain/no-rain 检测与 rain-rate 回归联合训练，缓解零雨样本占比高和强降水稀疏的问题。
3. **偏差校正与产品融合**：以 IMERG、TRMM、PERSIANN、CMORPH、GSMaP、SM2RAIN 等卫星降水产品为输入，通过 U-Net、ConvLSTM、Transformer 或 GAN 进行区域校正、融合和下采样。
4. **概率和生成式 QPE**：用分位数回归或生成式先验输出不确定性，代表是 Rain over Africa 和 PRISMA。

最值得优先精读的论文：

- Yang et al. 2023, TGRS：GOES ABI + GLM 两阶段 CNN。
- Gavahi et al. 2023, RSE：PDFN 多源降水融合。
- Dao et al. 2025, JGR Atmospheres：PDIR-Now 地形偏差校正。
- Amell et al. 2025, JGR Atmospheres：Rain over Africa 概率 QPE。
- Nguyen et al. 2026, JHM：PERSIANN-U-Net / PERSIANN V3。
- PRISMA 2026, arXiv：多卫星即插即用生成式 QPE。

## 2. 筛选口径

核心纳入条件：

- 任务是 QPE、precipitation estimation、retrieval、satellite precipitation fusion、bias correction 或 downscaling。
- 输出是当前/历史降水强度或累计量，而不是未来预报。
- 方法包含深度学习。
- 输入包含卫星观测或卫星降水产品。
- 来源是顶刊、强领域期刊、高质量会议或 arXiv 前沿预印本。

重要排除：

- 纯 radar QPE 不纳入。
- 纯 nowcasting/QPF 不纳入核心。
- 只做产品评估、不提出深度学习估计方法，不纳入核心。
- 只用 NWP 或再分析作为输入、卫星只是训练目标的论文，除非对 QPE 方法谱系有特殊意义，否则不列核心。

## 3. 核心论文表

| 年份 | 论文 | 来源 | 卫星输入 | 方法 | 核心价值 |
|---|---|---|---|---|---|
| 2023 | Deep Learning for Precipitation Retrievals Using ABI and GLM Measurements on the GOES-R Series | IEEE TGRS | GOES-R ABI + GLM | 两阶段 CNN | 直接改进 GOES RRQPE，证明 lightning 对强对流降水有增益 |
| 2023 | A deep learning-based framework for multi-source precipitation fusion | Remote Sensing of Environment | 遥感降水产品等多源数据 | 3D-CNN + ConvLSTM / PDFN | 顶级遥感期刊中系统建模 QPE 的时空融合 |
| 2023 | Multi-Task Learning for Simultaneous Retrievals of Passive Microwave Precipitation Estimates and Rain/No-Rain Classification | GRL | GPM PMW | 多任务神经网络 | rain/no-rain 与 rain-rate 联合学习，虽略早于严格窗口但非常关键 |
| 2025 | TRMM-era neural networks for GPM-era satellite QPE | Atmospheric Research | GPM-CO | 简洁 NN 对比现代 NN | 反思复杂模型与业务可部署性的关系 |
| 2025 | Bias Correction of Satellite Precipitation Estimation Using DNNs and Topographic Information | JGR Atmospheres | PDIR-Now | U-Net / Efficient-UNet / cGAN | 地形影响下卫星 QPE 偏差校正代表 |
| 2025 | Probabilistic Near-Real-Time Retrievals of Rain Over Africa Using Deep Learning | JGR Atmospheres | Meteosat thermal IR | CNN + quantile regression | 概率 QPE 与低延迟业务应用结合 |
| 2026 | PERSIANN-Unet | Journal of Hydrometeorology | 全球 GEO IR | U-Net | PERSIANN 系列的全球深度学习版本 |
| 2025 | HR-PrecipNet | Journal of Hydrology | IR/WV/SM 等 | U-Net variants + RF | 面向 1 km 高分辨率卫星 QPE |
| 2025 | Unsupervised adaptive fusion framework | Journal of Hydrology | IMERG-Early + SM2RAIN | adaptive fusion network | 无雨量站监督场景下的卫星 QPE 融合 |
| 2025 | Multi-task U-Net for Lancang-Mekong | Journal of Hydrology | PERSIANN-CDR + TRMM 3B42 | 多任务 U-Net | 区域多源卫星产品校正和融合 |
| 2026 | PRISMA | arXiv | multi-satellite modalities | latent generative model | 即插即用多传感器生成式 QPE |
| 2025 | Oya | arXiv | GEO VIS-IR | 两阶段 U-Net | 全球实时 QPE，显式处理降水样本不平衡 |
| 2025 | Huayu | arXiv | FY-4B IR | ML retrieval system | 东亚/中国区域 GEO 高分辨率实时 QPE |

## 4. 重点论文解读

### 4.1 GOES ABI + GLM 两阶段 CNN

Yang et al. 2023 直接面向 GOES-R operational RRQPE。模型输入包含 ABI 多通道云顶亮温和 GLM 闪电频次，结构上拆成 precipitation detection 与 precipitation quantification 两个 CNN。这个设计非常符合 QPE 的样本分布：大量无雨像元先由检测任务筛掉，再对有雨或可能有雨区域估计降水强度。

这篇论文的价值不只是“用了 CNN”，而是把 lightning 作为强对流信息补进 GEO IR QPE。IR 只能看到云顶，容易把冷云误判为降水，也可能漏掉暖雨过程；GLM 对深对流有辅助信号，因此对强降水和对流区域有直接意义。

### 4.2 RSE PDFN 多源时空融合

Gavahi et al. 2023 把 QPE 看成多源产品的时空融合问题，而不是单一传感器反演。PDFN 结合 3D-CNN 和 ConvLSTM，一边建模空间结构，一边建模时间依赖。这个方向对实际水文应用很重要，因为业务 QPE 很少只依赖一个来源，通常会融合雨量站、卫星、再分析和其他 gridded 产品。

这篇 RSE 论文可以作为“卫星 QPE 从 retrieval 走向 fusion”的关键节点。它也提示后续调研应关注：不同产品误差是否互补、时空依赖是否真的提升极端降水估计、融合模型是否跨气候区泛化。

### 4.3 被动微波多任务学习

Bannai et al. 2023 发表在 GRL，略早于严格三年窗口，但非常值得保留。它把 rain/no-rain 分类和 rain-rate 回归并行训练，而不是传统的先分类、再回归流水线。多任务学习可以让检测和定量估计共享表示，减少两个任务之间的误差传递。

这条路线对后续 Oya、Jiao et al. 的多任务 U-Net 都有启发：QPE 的核心困难之一不是简单回归，而是零雨样本和强降水长尾并存。分类-回归联合建模会成为深度学习 QPE 的基本设计模式。

### 4.4 JGR 地形偏差校正

Dao et al. 2025 关注 PDIR-Now 在美国西部复杂地形下的偏差，比较 U-Net、Efficient-UNet 和 cGAN。地形是卫星 QPE 中非常顽固的问题：山地降水过程受抬升、遮挡、云微物理和地表观测稀疏共同影响，单靠 IR 云顶亮温很难稳定估计近地面降水。

这篇论文的可复用点有三个：

- 把偏差校正视为 image-to-image translation。
- 将 DEM/topographic 信息作为辅助输入。
- 同时比较确定性 U-Net 和生成式 cGAN。

### 4.5 Rain over Africa 概率 QPE

Amell et al. 2025 提出 Rain over Africa，使用 Meteosat thermal IR，在 GPM 标定数据上训练 fully convolutional quantile regression network。它的核心不是只输出一个雨强值，而是输出概率分布或分位数，从而表达卫星 QPE 的不确定性。

这对非洲这类地面观测稀疏区域特别重要。QPE 不确定性如果能被显式输出，后续洪水预警、农业风险和水文模型就可以使用概率阈值，而不是只依赖单点估计。

### 4.6 PERSIANN-U-Net / PERSIANN V3

Nguyen et al. 2026 是 PERSIANN 家族的重要深度学习更新。它使用全球 GEO IR 图像和月尺度气候信息，通过 U-Net 生成近实时、准全球降水估计。和许多 patch-based 模型不同，PERSIANN-U-Net 强调全球一致性和减少拼接边界问题。

这篇论文适合放在“全球业务化 QPE 系统”的主线中。它也和 Oya、Huayu、RoA 形成对比：同样都是 GEO IR QPE，但覆盖范围、目标区域、训练目标、分辨率和不确定性表达不同。

### 4.7 Journal of Hydrology 融合与高分辨率路线

Journal of Hydrology 近三年的相关论文很多，主线是把卫星降水产品作为基础，再用深度学习校正、融合或下采样：

- HR-PrecipNet 用 U-Net 类模型生成初始 0.1° 降水，再用高分辨率变量细化到 1 km。
- 无监督 adaptive fusion 在没有雨量站监督的情况下融合 IMERG-Early 和 SM2RAIN。
- Lancang-Mekong 多任务 U-Net 用 PERSIANN-CDR、TRMM 3B42 和 ERA5 进行 bias correction 和 merging。

这些论文的共性是：不试图从原始卫星 radiance 端到端估计降水，而是把已有卫星降水产品作为输入，解决区域偏差、空间分辨率和水文可用性问题。

### 4.8 PRISMA、Oya、Huayu 等 arXiv 前沿

arXiv 上的新方向更激进：

- PRISMA 试图学习 IMERG Final 降水先验，再用不同传感器条件分支约束生成，目标是新传感器接入时不重训生成骨干。
- Oya 使用 GEO VIS-IR 全谱观测，两阶段 U-Net 处理降水检测和 QPE。
- Huayu 使用 FY-4B IR 进行 15 分钟、0.05° 实时降水估计，并与 IMERG Final 和雨量站比较。

这些论文尚需等待同行评审和独立复现，但从方法趋势看，生成式先验、多传感器模块化、实时高分辨率 GEO QPE 会是重要方向。

## 5. 技术路线总结

### 5.1 两阶段和多任务架构成为常态

QPE 的数据分布高度不平衡：无雨样本占多数，强降水样本稀少且误差代价高。因此，越来越多论文把任务拆成“是否降水”和“降水强度”，或者通过多任务学习共享表示。这个设计在 GOES ABI+GLM、GRL MTL、Oya、Jiao et al. 中都能看到。

### 5.2 GEO IR 的价值是时效，PMW/GPM 的价值是物理标签

GEO IR/VIS/WV 能提供分钟级或小时级连续覆盖，适合近实时 QPE；PMW 和 GPM DPR/GMI 与降水物理更直接，但时空采样不足。很多强论文实际上是在用 PMW/GPM/IMERG 训练或校准 GEO IR 模型。

### 5.3 从单点估计走向概率估计

Rain over Africa 用分位数回归输出不确定性，PRISMA 使用生成式框架表达降水先验。这说明 QPE 正在从“给一个最可能雨强”走向“给一个条件分布”。对风险应用而言，这是必要变化。

### 5.4 区域偏差校正仍然非常重要

即便有 IMERG、PERSIANN、CMORPH 等成熟产品，复杂地形、极端降水、暖雨、海陆过渡区仍然会出现系统偏差。深度学习在这里的角色不是替代卫星物理，而是学习区域误差结构和多源互补关系。

### 5.5 顶会和顶刊关注点不同

AI 顶会更偏预测、生成和大模型框架，气象/遥感顶刊更重视观测源、验证、物理合理性和业务可用性。本专题如果要写综述，不能只按 AI venue 排序；应以任务和观测体系为主线。

## 6. 后续精读顺序

1. Yang et al. 2023 TGRS：理解 GEO IR + lightning 的两阶段 retrieval。
2. Bannai et al. 2023 GRL：理解 rain/no-rain 与 rain-rate 多任务学习。
3. Gavahi et al. 2023 RSE：理解多源 QPE 融合的时空建模。
4. Dao et al. 2025 JGR：理解复杂地形 satellite QPE bias correction。
5. Amell et al. 2025 JGR：理解概率 QPE 和低延迟部署。
6. Nguyen et al. 2026 JHM：理解全球 PERSIANN-U-Net 系统。
7. PRISMA / Oya / Huayu：追踪生成式、多传感器、区域 GEO 实时估计的新方向。

## 7. 已核验来源

- Yang et al., 2023, TGRS: <https://repository.library.noaa.gov/view/noaa/61892>
- Gavahi et al., 2023, RSE: <https://www.sciencedirect.com/science/article/pii/S0034425723002742>
- Bannai et al., 2023, GRL: <https://pure.kaist.ac.kr/en/publications/multi-task-learning-for-simultaneous-retrievals-of-passive-microw/>
- Leganés et al., 2025, Atmospheric Research: <https://research.knu.ac.kr/en/publications/trmm-era-neural-networks-for-gpm-era-satellite-quantitative-preci/>
- Dao et al., 2025, JGR Atmospheres: <https://agupubs.onlinelibrary.wiley.com/doi/10.1029/2024JD042181>
- Amell et al., 2025, JGR Atmospheres: <https://research.chalmers.se/en/publication/548867>
- Nguyen et al., 2026, JHM: <https://journals.ametsoc.org/view/journals/hydr/aop/JHM-D-25-0162.1/JHM-D-25-0162.1.xml>
- Mosaffa et al., 2025, Journal of Hydrology: <https://www.sciencedirect.com/science/article/pii/S0022169425005554>
- Liu et al., 2025, Journal of Hydrology: <https://www.sciencedirect.com/science/article/pii/S0022169424017372>
- Jiao et al., 2025, Journal of Hydrology: <https://www.sciencedirect.com/science/article/pii/S0022169425013642>
- PRISMA, 2026, arXiv: <https://arxiv.org/abs/2605.14426>
- Oya, 2025, arXiv: <https://arxiv.org/abs/2511.10562>
- Huayu, 2025, arXiv: <https://arxiv.org/abs/2512.15222>
- AAAI 2025 adjacent nowcasting paper: <https://ojs.aaai.org/index.php/AAAI/article/view/35049>

