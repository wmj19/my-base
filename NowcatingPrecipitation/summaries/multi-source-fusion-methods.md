# 多源融合降水临近预报方法整理

整理日期：2026-06-04  
范围：基于 `NowcatingPrecipitation/literature/` 已收集材料的阶段性整理。本文未重新联网核验论文全文；对只来自摘要、元数据或日报概括的细节，均按“待核验”处理。

## 1. 问题定位

雷达-only 临近预报擅长刻画已有回波的平移、形变和短时强度变化，但在这些场景下容易失效：

- **对流初生**：雷达还没有明显回波，单靠历史回波难以提前捕捉云顶冷却、水汽聚集和热力不稳定。
- **强降水维持和消散**：MSE/MAE 训练常把强回波平均掉，长 lead time 下会出现强度衰减和边界模糊。
- **遮挡、地形和观测偏差**：雷达拼图/QPE 可能受地物杂波、波束遮挡、站网密度和定量估计误差影响。
- **0-3 h 甚至更长时效**：超过传统 0-2 h 区间后，大尺度环境、水汽和背景环流信息越来越重要。

因此，多源融合的核心不是简单“多加几个通道”，而是回答三个问题：

1. **补什么信息**：卫星补云顶/水汽/对流先兆，站点和雨量计补地面实况，GNSS 补水汽，NWP/基础模型补大尺度背景，三维/双偏振雷达补垂直结构和微物理。
2. **在哪里融合**：输入早期拼接、双流编码后融合、cross-attention 对齐、扩散/生成过程条件化、后处理/订正阶段融合。
3. **预测什么目标**：未来雷达回波/VIL、未来雷达 QPE/降水场、雷达代理图像，或强降水等级/概率。

## 2. 方法谱系总览

| 方法类别 | 典型输入 | 融合位置 | 代表条目 | 适合解决的问题 |
|---|---|---|---|---|
| 雷达-卫星双流融合 | 雷达 VIL/反射率 + IR/WV/BTD/可见光/闪电 | 双编码器、通道/空间注意力、cross-attention | DA-RNN、MS-FTNet、MAG-Net、M4Caster | 对流初生、云团发展、长时效退化 |
| 雷达-卫星生成式融合 | 雷达序列 + 卫星序列 | 潜空间扩散、残差扩散、卫星条件化去噪 | CastDiffuser、VMU-Diff、RSG-GAN | 高分辨率细节、强回波边界、概率预报 |
| Satellite-to-radar / 雷达代理 | 卫星 IR/lightning 等，目标为未来 VIL/雷达图 | Transformer 或神经算子先预测卫星/雷达代理 | EF Sat2Rad、PIANO | 雷达缺测区域、弱雷达覆盖区域 |
| 雷达-站点/雨量计融合 | 雷达回波/QPE + 地面站/雨量计 | 双分支 RNN、订正网络、多模态 loss | MMF-RNN、RN-Net、RST-RainNet、qPrec/FlowsNet | 地面降水订正、强降水漏报、山洪应用 |
| 雷达-GNSS 水汽融合 | 雷达 QPE + GNSS PWV/ZTD + 卫星 | 门控跨模态融合、GAN | FusionCast、RSG-GAN | 水汽先兆、强降水触发、0-2 h 到 0-3 h |
| 雷达-NWP/基础模型先验 | 雷达 + Pangu-Weather/NWP/风场/环境变量 | 频谱融合、运动估计、条件输入 | PW-FouCast、Nwf-Net、FlowsNet | 3 h 附近长时效、大尺度背景约束 |
| 三维/双偏振雷达多变量融合 | ZH/ZDR/KDP、多高度反射率、Doppler wind | late fusion、跨变量特征融合、物理损失 | FURECast、SwinKAN、热带 DGMR 案例 | 强对流、垂直发展、微物理一致性 |
| 雷达-文本/任务提示多模态 | 雷达序列 + 运动描述/任务提示/CoT | 共享主干、任务特定解码、语言条件生成 | LangPrecip、Omni-Weather | 可解释生成、多任务雷达理解 |
| 融合产品上的 nowcasting | 已融合的 qPrec、radar-gauge analyzed precipitation | 模型输入即融合降水场，再用 U-Net/EarthFormer 等预测 | qPrec Slovakia、FlowsNet | 业务降水场、山洪预警、端到端应用 |

## 3. 雷达-卫星融合

这是当前材料里最密集的一条线。基本假设是：雷达给出已经发生或正在发展的降水结构，卫星给出云顶温度、水汽通道、亮温差和对流发展先兆。

### 3.1 早期/中期注意力融合

**DA-RNN** 使用 SEVIR 的雷达 VIL 与卫星红外数据，在 ConvLSTM/TrajGRU 骨架上加入空间注意力和通道注意力。它代表较直接的 RNN 多源融合路线：主干仍是经典时空循环模型，多源收益主要来自注意力筛选互补通道。

**MS-FTNet** 使用 SEVIR 的雷达 VIL 与卫星红外，采用双编码器、Global Feature Collaboration 和 Adaptive Temporal Fusion。它的特点是把多源融合和 Fourier 时序建模结合：低频偏向大尺度稳定背景，高频偏向局地对流、边界和突变。适合放在“雷达-卫星融合 + 频域建模”交叉方向。

**MAG-Net** 面向中国东南部强对流，融合 radar dynamics 与静止卫星 IR/WV/BTD 通道。它的设计更强调物理含义：雷达描述已有回波动力学，卫星通道补充云顶、水汽和热力先兆；双头解码同时做反射率回归和强对流事件概率分类，推理阶段再做 Gradient-Preserving Fusion。该文的细节目前来自本地日报，需后续阅读全文核验。

**M4Caster** 使用长三角/江苏区域的卫星观测和 S-band 地基雷达复合反射率，预测未来雷达回波序列。方法包含 multi-spatial and multi-temporal aggregator 以及双向 cross-attention 桥接融合，重点解决局部细节、全局上下文和对流初生。由于区域数据集不属于公开重点基准，横向比较需要谨慎。

### 3.2 生成式雷达-卫星融合

**CastDiffuser** 是“确定性时空翻译 + 卫星条件潜空间扩散”的级联结构。它先在潜空间学习高分辨率雷达演变，再用 Satellite-Radar Diffusion Transformer 细化未来雷达序列。优势是适合高分辨率业务雷达图，风险是 VAE 压缩可能损失强回波峰值，且江苏区域数据的复现性有限。

**VMU-Diff** 采用粗到细框架：第一阶段用 Vision Mamba U-Net 融合历史雷达和多波段卫星，生成粗尺度运动趋势；第二阶段用残差条件扩散补偿高频细节。它把“多源融合、状态空间模型、残差扩散”放在同一个框架中，适合跟 CastDiffuser 对比：一个偏 Transformer/潜空间扩散，一个偏 Mamba/粗到细残差。

**RSG-GAN** 融合 Radar QPE、GOES-16 split window difference 和 GNSS ZTD，并用 GAN 生成未来降水。它比普通雷达-卫星双源多了 GNSS 水汽约束，也在 SEVIR 上做迁移学习实验。结构细节目前主要来自摘要级记录，待 IEEE 原文核验。

### 3.3 Satellite-to-radar 与雷达代理

**Transformer-Based Nowcasting of Radar Composites from Satellite Images for Severe Weather** 将 SEVIR 中的卫星/闪电信息映射到未来 NEXRAD VIL 雷达图，属于 satellite-to-radar，而不是传统 radar-to-radar extrapolation。它的价值在于缺雷达区域或雷达覆盖弱区域可以生成雷达代理。

**PIANO** 的思路更“物理链条化”：先用 physics-informed dual neural operator 预测卫星图像，再通过生成模型转换为 radar images，用于 precipitation nowcasting。它适合作为“缺雷达区域如何借助卫星生成雷达代理”的补充方向。

## 4. 雷达-站点、雨量计和 GNSS 融合

这条线更接近业务降水场，而不是纯视觉式雷达回波外推。

**MMF-RNN** 使用双分支编码器分别处理雷达和地面站数据，再通过注意力融合；同时提出 BDWLoss 和 multimodal loss，分别处理难预测区域和单模态/融合表征不一致。它可嵌入 ConvLSTM、PredRNN、PredRNN++、MIM 等循环主干，是 radar-station fusion 的近期 TGRS 案例。

**Deep learning-based precipitation nowcasting integrating radar echoes and rain gauge data** 使用 SAAR-UNet 做雷达回波外推，再用 RST-RainNet 结合雨量计进行订正。这个两阶段思路很实用：雷达负责空间结构，雨量计负责地面实况校正。

**RN-Net** 是较早的 0-2 h 雷达和自动气象站融合案例，可作为“低成本地面观测补充雷达外推”的背景样本。它的模型族较老，但问题设定仍有参考价值。

**FusionCast** 融合 GNSS PWV 与 radar-based QPE，并引入 forecasted radar QPE future prior。PWV 提供降水前水汽条件，QPE 提供已发生降水结构；它代表“雷达衍生降水 + 水汽观测”的不对称跨模态融合路线。待核验点是 future prior 是否带来级联误差。

**RSG-GAN** 也属于这条线，因为它同时使用 radar QPE、GOES-16 SWD 和 GNSS ZTD。与 FusionCast 相比，它更偏三源 GAN 生成式框架。

## 5. 雷达-NWP、基础模型和环境先验融合

这类方法试图弥补雷达外推对大尺度环境的缺失，尤其适用于 2-6 h、复杂地形或准静止强降水。

**PW-FouCast** 融合雷达观测与 Pangu-Weather 先验，并在 SEVIR 和 MeteoNet 上评估。它的方向不是简单加入 NWP 网格，而是在频谱层面融合雷达细尺度观测和基础模型大尺度背景，适合放在“foundation model prior + radar nowcasting”的新路线中。

**Nwf-Net** 面向阿尔卑斯强降水，MET 模块结合高空风场和传统光流表示雷达回波位移/形态变化，RIC 模块用深度循环网络订正强对流强度。它是“光流/物理运动估计 + 深度学习强度修正”的混合路线。

**FlowsNet** 号称 NWP-free，但输入本身是多源观测：radar/rain-gauge-analyzed precipitation、地面站变量、地球同步卫星图像和卫星降水背景。它把目标扩展到 3 h 以外，用 channel-wise attention、spatial attention 和 tail-aware ordinal loss 面向强降水等级预警。该条目前为 EGU 摘要，需核验完整网络和数据切分。

## 6. 三维、双偏振和多雷达变量融合

这里的“多源”不是跨平台，而是同一雷达系统内的多变量、多高度、多极化信息。它对强对流尤其重要，因为垂直结构和微物理变量能提供对流发展信号。

**FURECast** 使用三维双偏振雷达变量 ZH、ZDR、KDP，并加入 KDP 与 ZH/ZDR 经验关系的自洽损失。它的贡献是把双偏振微物理一致性写进训练目标，而不是只把变量当作普通通道。

**SwinKAN** 使用双偏振雷达变量 Zh、Zdr、Kdp，同时预测多个变量，并通过 Swin Transformer、KAN 和 Cross-Feature Fusion Module 建模跨变量交互。它适合和 FURECast 对比：一个强调物理损失，一个强调跨变量特征表达。

**TriPhysGAN-Attn** 使用 NJU-CPOL 双偏振雷达数据，将演变机制拆成平流、生成/消散、形变三个分支，并用 cross-attention 融合。这不只是多变量融合，也是物理机制分解融合。

**热带东南亚 DGMR 类案例** 使用高分辨率雷达图像、Doppler wind、多高度反射率、复合反射率和时间体制信息。日报记录显示 multi-altitude reflectivity 对 skill 贡献最大。由于目前是 EGU 摘要，应作为待跟踪候选。

## 7. 融合产品上的临近预报

有些工作并不在模型内部显式融合多源，而是直接使用已经融合好的降水产品。这类方法适合业务应用，但不容易解释每个观测源的独立贡献。

**qPrec Slovakia operational / flash-flood nowcasting** 使用融合 radar observations、satellite observations 和 in-situ stations 的 qPrec 高分辨率实时降水场，再用 U-Net、Earthformer 等模型做 nowcasting。它适合讨论山洪预警场景，但公平比较纯雷达外推模型时要小心。

**FlowsNet** 也可归入这一类，因为核心输入之一是 radar/rain-gauge-analyzed precipitation，属于雷达和雨量计分析降水产品。

## 8. 雷达-文本和基础多模态模型

这条线目前还不是业务主流，但适合跟踪，因为它把降水临近预报从单任务图像预测推向“雷达理解 + 生成 + 解释”的统一模型。

**LangPrecip** 融合文本和雷达信息，构建配对雷达序列与运动描述的数据，用语言条件帮助模型理解移动方向、形态变化和强度演变。它适合放在“language-aware multimodal nowcasting”方向，但需要核验文本描述的生成方式、是否会引入标注偏差，以及对常规 CSI/HSS 是否真正有增益。

**Omni-Weather** 使用统一多模态天气模型处理 SEVIR time-aligned radar and satellite sequences，其中雷达 nowcasting 任务给定 10 帧 VIL 预测后续 12 帧。它的价值在于把雷达 nowcasting、天气图像生成和任务提示放到统一主干中，而不是只训练一个专用外推模型。

## 9. 关键技术模式

### 9.1 早期拼接

把多源变量重采样到同一网格后按通道拼接。优点是实现简单、易接入现有 U-Net/ConvLSTM；缺点是不同源的空间分辨率、时间延迟和物理含义容易被模型混在一起，解释性差。

适合：变量已经严格配准、模态差异不大、先做 baseline。

### 9.2 双流编码 + 注意力融合

雷达和卫星/站点/GNSS 分别编码，再用 channel attention、spatial attention、cross-attention 或门控模块融合。DA-RNN、MAG-Net、M4Caster、MMF-RNN、FusionCast 都可放在这个模式下。

适合：模态异构明显，需要保留各自表示，再做可解释的互补。

### 9.3 多尺度/频域融合

用 Fourier、小波、频谱损失或多尺度 patch 聚合区分低频背景与高频对流细节。MS-FTNet、PW-FouCast、AlphaPre、WADEPre、FADiff 等与该线相关，其中 MS-FTNet 和 PW-FouCast 更直接涉及多源融合。

适合：强回波边界、长 lead time 模糊和大尺度背景补偿。

### 9.4 生成式条件融合

把额外模态作为扩散、flow、GAN 或 latent diffusion 的条件，用于生成未来细节或概率分布。CastDiffuser、VMU-Diff、RSG-GAN 属于这个模式。

适合：需要概率预报、高分辨率细节、多可能未来；风险是推理成本、校准和伪影。

### 9.5 两阶段外推 + 订正

先用雷达/光流/确定性模型给出粗预报，再用多源信息订正强度、残差或地面降水。CastDiffuser、VMU-Diff、Nwf-Net、RST-RainNet 都有类似思想。

适合：已有业务外推链路，想渐进式加入深度学习或多源订正。

## 10. 选型建议

如果目标是**公开基准可比性**，优先从 SEVIR 的雷达 VIL + 卫星 IR 入手，代表模型包括 DA-RNN、MS-FTNet、EF Sat2Rad，并与 DiffCast、AlphaPre、DuoCast 等 radar-only 或单目标基线比较。

如果目标是**中国区域业务高分辨率雷达预报**，优先关注 CastDiffuser、VMU-Diff、M4Caster、MAG-Net。这些方法更贴近江苏、长三角、东南中国等区域业务数据，但需要特别标注数据不可公开、预处理不统一和复现困难。

如果目标是**强对流/极端降水**，卫星 WV/BTD、GNSS PWV/ZTD、双偏振 ZDR/KDP、多高度反射率和 tail-aware loss 都值得纳入。可重点比较 MAG-Net、FURECast、TriPhysGAN-Attn、RSG-GAN、FlowsNet。

如果目标是**业务落地和可维护性**，两阶段方法更现实：雷达或光流先外推，站点/雨量计/GNSS/卫星再订正。MMF-RNN、RST-RainNet、Nwf-Net、qPrec/FlowsNet 比全端到端大模型更容易解释和灰度部署。

## 11. 后续待核验问题

- 这些多源方法是否在相同 lead time、分辨率、阈值和降水单位下可比？多数区域数据集不可直接横比。
- 多源收益是否有严格消融：radar-only、satellite-only、radar+satellite、radar+satellite+GNSS/站点。
- 卫星/GNSS/站点收益是否随 lead time 增加而增强？尤其 30、60、90、120 min 阶段应分开看。
- 多源融合是否改善强降水 CSI/POD 的同时增加 FAR？需要阈值指标和可靠性指标一起看。
- 站点/GNSS 网络密度变化时，模型是否还能泛化？
- 对业务系统而言，卫星、雷达、站点和 NWP 的时间延迟是否会抵消模型收益？

## 12. 参考入口

- 文献总览：[近三年深度学习临近降水预报论文/模型调研](../literature/2026-05-12_recent-3y-dl-precip-nowcasting.md)
- 文献索引：[paper-index.json](../literature/paper-index.json)
- 相关日报：
  - [2026-05-13](../literature/daily/2026-05-13.md)：qPrec、多源业务山洪应用
  - [2026-05-14](../literature/daily/2026-05-14.md)：MS-FTNet、CastDiffuser、TriPhysGAN-Attn
  - [2026-05-16](../literature/daily/2026-05-16.md)：VMU-Diff、MAG-Net、FusionCast、FURECast
  - [2026-05-20](../literature/daily/2026-05-20.md)：DA-RNN、satellite-to-radar、PW-FouCast
  - [2026-05-21](../literature/daily/2026-05-21.md)：PIANO 等雷达代理方向
  - [2026-05-29](../literature/daily/2026-05-29.md)：RSG-GAN、M4Caster、LangPrecip
  - [2026-05-30](../literature/daily/2026-05-30.md)：FlowsNet、物理信息生成式热带降水案例
  - [2026-06-04](../literature/daily/2026-06-04.md)：MMF-RNN、Nwf-Net
