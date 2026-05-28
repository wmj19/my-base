# Satellite-input deep learning QPE index

最后更新：2026-05-28

## 入口

- 深度综述：[surveys/2026-05-28_satellite-dl-qpe-top-venues-survey.md](surveys/2026-05-28_satellite-dl-qpe-top-venues-survey.md)
- 结构化论文索引：[literature/paper-index.json](literature/paper-index.json)
- 数据源说明：[datasets/satellite-qpe-datasets.md](datasets/satellite-qpe-datasets.md)
- 筛选标准：[venues/top-venue-screening.md](venues/top-venue-screening.md)
- 后续待办：[backlog.md](backlog.md)

## 核心论文主线

### 1. 直接卫星反演 / retrieval

- Yang et al., 2023, IEEE TGRS：GOES-R ABI + GLM，两阶段 CNN 做降水检测和定量反演。
- Bannai et al., 2023, GRL：GPM 被动微波，多任务学习同时做 rain/no-rain 和 rain-rate retrieval。
- Leganés et al., 2025, Atmospheric Research：比较 TRMM 时代神经网络和现代 TensorFlow NN 在 GPM 时代 QPE 中的表现。
- Amell et al., 2025, JGR Atmospheres：Rain over Africa，用 Meteosat 热红外和概率分位数 CNN 做近实时非洲降水反演。
- Nguyen et al., 2026, Journal of Hydrometeorology：PERSIANN-U-Net / PERSIANN V3，用全球 IR 图像和 U-Net 生成近实时全球降水估计。
- Oya, 2025, arXiv：全球 GEO VIS-IR 输入，两阶段 U-Net 做降水检测和 QPE。
- Huayu, 2025, arXiv：FY-4B IR 近实时高分辨率降水估计。

### 2. 产品融合 / 偏差校正 / downscaling

- Gavahi et al., 2023, Remote Sensing of Environment：PDFN，用 3D-CNN + ConvLSTM 融合多源降水产品。
- Liu et al., 2025, Journal of Hydrology：无雨量站监督的卫星降水自适应融合，融合 IMERG-Early 和 SM2RAIN。
- Dao et al., 2025, JGR Atmospheres：PDIR-Now 偏差校正，比较 U-Net、Efficient-UNet、cGAN。
- Mosaffa et al., 2025, Journal of Hydrology：HR-PrecipNet，两步框架实现 1 km 高分辨率卫星降水估计。
- Jiao et al., 2025, Journal of Hydrology：多任务 U-Net，对 PERSIANN-CDR、TRMM 3B42 和 ERA5 做偏差校正和融合。
- PRISMA, 2026, arXiv：多卫星即插即用生成式框架，学习 IMERG Final 降水先验并接入不同传感器分支。

## 重要边界

- QPE 是估计当前或历史降水；QPF/nowcasting 是预测未来降水。
- AAAI 2025 的 “Data-driven Precipitation Nowcasting Using Satellite Imagery” 属于卫星输入的降水预报，不是严格 QPE；可借鉴模型和数据处理，但不放入核心 QPE 清单。
- 部分 2026 论文已经在线或预印本出现，但后续 DOI、正式刊期、代码和数据状态可能变化，需要继续核验。

