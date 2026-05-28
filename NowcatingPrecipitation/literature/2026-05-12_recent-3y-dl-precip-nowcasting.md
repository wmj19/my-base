# 近三年深度学习临近降水预报论文/模型调研

调研时间：2026-05-12  
覆盖范围：约 2023-05 至 2026-05  
主题：使用深度学习进行降水临近预报、雷达回波外推、短时强降水预报的论文与模型。优先收录实验中明确使用雷达回波、天气雷达反射率、雷达衍生降水图或 radar-satellite fusion 数据的工作。

## 1. 数据集口径说明

### 1.1 CIKM

这里的 CIKM 通常指 CIKM AnalytiCup 2017 短时降水/雷达回波预测比赛数据，任务是基于过去雷达回波图预测未来回波图。近三年的多篇基准型论文把 CIKM 与 SEVIR、Shanghai、MeteoNet 一起作为雷达回波 nowcasting benchmark。

### 1.2 Shanghai / ShangHai

Shanghai Radar Dataset 通常指上海地区多普勒天气雷达回波数据，常被用来评估中国区域强对流和城市尺度降水临近预报。DiffCast、AlphaPre、DuoCast、WADEPre 等工作都把 Shanghai 作为核心评测集之一。

### 1.3 SEVIR

SEVIR 是 Storm EVent ImagRy 数据集，包含多通道卫星图像、NEXRAD derived VIL 等观测，常用于 radar/satellite fusion nowcasting。近三年几乎所有生成式 nowcasting 工作都会在 SEVIR 上报告结果，因此它已经成为深度学习降水临近预报的重要公开基准。

### 1.4 Metnet / MeteoNet 的歧义

用户提到的 “Metnet 数据集” 需要区分两种含义：

- **MeteoNet**：法国开放气象数据集，包含雷达、雨量计、卫星、NWP 等资料。近三年 benchmark 论文中常与 SEVIR、Shanghai、CIKM 一起出现，通常才是“数据集”意义上的对象。
- **MetNet / MetNet-2 / MetNet-3 / Global MetNet**：Google 的深度学习预报模型家族，不是单一公开 benchmark 数据集。相关论文更多是业务级模型和大区域预报系统。

因此，下文重点把 “Metnet” 按 **MeteoNet 数据集**处理，同时单独列出 MetNet 模型家族，避免后续写综述时混淆。

## 2. 与四个目标数据集直接相关的核心论文

| 年份 | 论文/模型 | 来源 | 使用的数据集 | 方法类型 | 重点结论 |
|---|---|---|---|---|---|
| 2024 | [DiffCast: A Unified Framework via Residual Diffusion for Precipitation Nowcasting](https://openaccess.thecvf.com/content/CVPR2024/html/Yu_DiffCast_A_Unified_Framework_via_Residual_Diffusion_for_Precipitation_Nowcasting_CVPR_2024_paper.html) | CVPR 2024 | SEVIR、MeteoNet、Shanghai、CIKM | 残差扩散；确定性预测 + 随机残差生成 | 四个目标数据集全部覆盖，是当前最适合作为“统一基准”引用的论文之一。 |
| 2025 | [AlphaPre: Amplitude-Phase Disentanglement Model for Precipitation Nowcasting](https://openaccess.thecvf.com/content/CVPR2025/html/Lin_AlphaPre_Amplitude-Phase_Disentanglement_Model_for_Precipitation_Nowcasting_CVPR_2025_paper.html) | CVPR 2025 | SEVIR、MeteoNet、Shanghai、CIKM | 傅里叶幅度-相位解耦；时空预测 | 强调模糊问题来自幅度和相位混叠，四个目标数据集全部覆盖。 |
| 2026 | [DuoCast: Duo-Probabilistic Diffusion for Precipitation Nowcasting](https://ojs.aaai.org/index.php/AAAI/article/view/41294) | AAAI 2026 / arXiv 2024 | SEVIR、MeteoNet、Shanghai、CIKM | 低频/高频双扩散；结构一致性 + 细节保持 | 显式分解大尺度趋势与细尺度变化，适合放在概率生成模型和频域分解方向。 |
| 2026 | [WADEPre: A Wavelet-based Decomposition Model for Extreme Precipitation Nowcasting](https://arxiv.org/abs/2602.02096) | arXiv 2026 | SEVIR、Shanghai 等 | 小波分解；极端降水增强 | 面向极端降水，高频细节保持是其主要卖点；与强回波衰减问题高度相关。 |
| 2024 | [CasCast: Skillful High-resolution Precipitation Nowcasting via Cascaded Modelling](https://proceedings.mlr.press/v235/nie24a.html) | ICML 2024 | SEVIR、MeteoNet、HKO-7 | 确定性预测 + 扩散修正的级联框架 | 不覆盖 CIKM/Shanghai，但覆盖 SEVIR 和 MeteoNet，是高分辨率概率 nowcasting 的重要工作。 |
| 2023 | [PreDiff: Precipitation Nowcasting with Latent Diffusion Models](https://proceedings.neurips.cc/paper_files/paper/2023/hash/f86a2e5b9f75b07c5b38c8f9f3c81a4b-Abstract-Conference.html) | NeurIPS 2023 | SEVIR | 潜空间扩散；知识对齐 | 较早把 diffusion 引入 SEVIR 降水临近预报，常作为生成式 nowcasting 起点引用。 |
| 2024 | [FACL: Fourier Amplitude and Correlation Loss for Skillful Precipitation Nowcasting](https://openreview.net/forum?id=CgaN1eqZdn) | NeurIPS 2024 | SEVIR、MeteoNet、HKO-7 | 损失函数；频域幅度 + 相关性约束 | 不是新架构，而是从训练目标解决模糊和强降水衰减；可与多种 backbone 结合。 |
| 2026 | [FlowCast: Advancing Precipitation Nowcasting with Conditional Flow Matching](https://openreview.net/forum?id=7rPqx2ERWg) | ICLR 2026 | SEVIR、ARSO | Conditional Flow Matching | 相比扩散模型，使用 flow matching 生成未来降水场；适合放在扩散之后的新生成模型路线。 |
| 2026 | [MS-FTNet: A Multisource Spatiotemporal Prediction Network for Precipitation Nowcasting](https://www.mdpi.com/1424-8220/26/9/2868) | Sensors 2026 | SEVIR | 多源时空融合；傅里叶时序模块 | 使用 SEVIR 的多源观测，关注卫星红外与雷达 VIL 的互补信息。 |

## 3. 重点论文笔记

### 3.1 DiffCast, CVPR 2024

DiffCast 的核心思想是把 nowcasting 拆成两部分：先用确定性模型产生粗预测，再用条件扩散模型生成残差，从而修正模糊、强度衰减和不确定性问题。论文明确在 **SEVIR、MeteoNet、Shanghai、CIKM** 四个 benchmark 上实验，因此特别适合作为本课题整理四个目标数据集的主线论文。

值得注意的是，DiffCast 的定位不是完全替代 ConvLSTM/PredRNN/SimVP 一类 backbone，而是把残差扩散作为统一修正框架。这意味着后续论文中如果有“deterministic forecast + generative correction”的结构，基本都可以和 DiffCast 建立联系。

### 3.2 AlphaPre, CVPR 2025

AlphaPre 从频域角度解释降水临近预报中的模糊问题：幅度信息与相位信息承担不同角色，直接在空间域建模会造成强回波边界和运动结构退化。模型将幅度和相位解耦建模，并在 **SEVIR、MeteoNet、Shanghai、CIKM** 上验证。

这篇论文和 FACL 可以一起读：FACL 主要改 loss，AlphaPre 主要改模型结构，二者都指向一个共同问题，即深度模型在长预报步长下容易损失高频结构、强降水核心和边界清晰度。

### 3.3 DuoCast, AAAI 2026 / arXiv 2024

DuoCast 把降水临近预报分解为低频和高频两个部分：低频分支捕获大尺度趋势和结构一致性，高频分支细化局地变化和边界细节。AAAI 2026 论文页说明其在四个 benchmark radar dataset 上超过现有方法；公开全文检索结果显示其评测集包括 **SEVIR、MeteoNet、Shanghai、CIKM**。

如果论文综述要讨论“概率预报”或“生成式模型”，DuoCast 可以作为 DiffCast 之后的进一步发展：DiffCast 强调残差扩散，DuoCast 进一步把降水场分解到低频大尺度趋势和高频细节两个子空间。

### 3.4 WADEPre, arXiv 2026

WADEPre 面向极端降水场景，使用小波分解把低频背景和高频细节分开处理。对雷达回波 nowcasting 来说，这个方向很重要，因为极端降水通常对应局地强回波、高梯度边界和快速发展结构，而常规 MSE/L1 训练容易把这些细节平均掉。

目前它应作为候选前沿论文跟踪。若后续要建立实验基线，可以优先检查其代码是否公开、Shanghai 与 SEVIR 的预处理是否与 DiffCast/AlphaPre 一致。

### 3.5 CasCast, ICML 2024

CasCast 是高分辨率降水临近预报中很有代表性的级联框架。它先进行确定性预测，再通过扩散模型补充高频和概率性细节。相比只输出单一未来序列的模型，CasCast 更强调概率分布和多样性。

它的实验覆盖 **SEVIR、MeteoNet、HKO-7**。虽然没有覆盖 CIKM 和 Shanghai，但它对生成式 nowcasting 的影响很大，建议在综述中与 DiffCast、PreDiff、DuoCast 一起讨论。

### 3.6 PreDiff, NeurIPS 2023

PreDiff 是近三年扩散模型进入降水临近预报的重要早期工作之一。它使用潜空间扩散模型，并尝试引入领域知识约束，实验主要基于 **SEVIR**。它的价值在于证明 diffusion 不只是图像生成工具，也能用于雷达 VIL/降水序列的概率预测。

与后续 DiffCast、CasCast 相比，PreDiff 更像生成式路线的开端；后续工作则逐步强调残差修正、级联预测、频域约束和不确定性分解。

### 3.7 FACL, NeurIPS 2024

FACL 的贡献在 loss 设计，而不是提出一个全新 backbone。它指出传统像素级损失容易导致降水场过度平滑，因此引入 Fourier amplitude loss 和 correlation loss 来增强频域结构和空间相关性。实验覆盖 **SEVIR、MeteoNet、HKO-7**。

这篇论文适合放在“训练目标与评价指标”部分。对本课题很有启发的一点是：如果目标关注强降水和极端事件，仅靠换模型结构可能不够，还需要调整损失函数、采样策略和阈值型评价指标。

### 3.8 FlowCast, ICLR 2026

FlowCast 使用 conditional flow matching 进行降水临近预报，代表了 diffusion 之外的另一类连续生成模型路线。它在 **SEVIR** 和奥地利 ARSO 雷达数据上评估，并把目标放在生成高质量概率预报上。

在综述结构中，可以把 FlowCast 放在 “diffusion/flow-based probabilistic nowcasting” 小节：PreDiff 和 DiffCast 是 diffusion 路线，FlowCast 则说明该领域正在从扩散采样进一步扩展到 flow matching。

### 3.9 MS-FTNet, Sensors 2026

MS-FTNet 是一篇多源融合方向的新论文，使用 **SEVIR** 的多源观测，尤其关注卫星红外通道与雷达 VIL 的融合。其结构包括多源空间特征提取、傅里叶时序建模和注意力融合。

它的意义在于贴近用户最初提到的“多源融合”方向。若后续研究不仅使用雷达回波，还希望引入卫星、NWP 或物理变量，MS-FTNet 可作为近期参考。

## 4. 其他应纳入综述主线的近三年代表模型

| 年份 | 论文/模型 | 链接 | 数据来源 | 研究方向 | 说明 |
|---|---|---|---|---|---|
| 2023 | NowcastNet | [Nature: Skilful nowcasting of extreme precipitation with NowcastNet](https://www.nature.com/articles/s41586-023-06184-4) | 中国和美国雷达观测 | 物理条件神经网络；极端降水 | 不是四个 benchmark 数据集，但它是“物理机制 + 深度生成”方向的标志性论文。 |
| 2025 | Global MetNet | [Nature: A generative AI model for global high-resolution weather prediction](https://www.nature.com/articles/s41586-025-08897-0) | 全球多源观测、ERA5、IMERG 等 | 全球高分辨率生成式预报 | 这是 MetNet 模型家族，不是 MeteoNet 数据集；可用于区分模型名与数据集名。 |
| 2026 | Efficient deep learning for radar precipitation nowcasting using spatiotemporal encoding and two-dimensional reconstruction | [EGU 2026 摘要](https://doi.org/10.5194/egusphere-egu26-20598) | RYDL / German Weather Service radar composite | 3D 时空编码 + 2D 重建 | 本地已有日报收录，偏工程高效雷达预报。 |
| 2026 | Optical Flow with RAFT for weather radar nowcasting | [EGU 2026 摘要](https://doi.org/10.5194/egusphere-egu26-7614) | 丹麦 C-band weather radar | 深度光流 + 外推 | 可作为传统外推和深度学习结合的代表。 |

## 5. 近三年技术路线总结

### 5.1 从 ConvLSTM 到统一时空预测 backbone

ConvLSTM 是该领域的起点之一，之后 PredRNN、MIM、SimVP、EarthFormer 等模型不断改进时空表征能力。近三年论文中，这些模型常作为 backbone 或强基线出现，但前沿贡献逐渐从“单纯换 backbone”转向“生成式修正、频域约束、多源融合和物理约束”。

### 5.2 生成式模型成为主线

PreDiff、DiffCast、CasCast、DuoCast、FlowCast 都属于这一大方向。它们关注的问题是确定性模型在长 lead time 下容易产生模糊、低估极端降水和缺乏不确定性表达。扩散模型和 flow matching 的优势在于可以生成多个可能未来，从而更适合降水这种强随机、强非线性的过程。

### 5.3 频域和多尺度建模越来越重要

FACL、AlphaPre、WADEPre 都说明，强降水核心和回波边界往往体现在高频结构中。像素级损失会导致高频细节衰减，因此近年工作开始使用 Fourier loss、幅相解耦、小波分解等方式保持空间结构。

### 5.4 多源融合正在从“可选增强”变成“核心能力”

SEVIR 这类数据集推动了卫星、雷达、闪电、VIL 等多源资料融合。MS-FTNet 等论文说明，模型不再只依赖单一雷达回波序列，而是尝试利用不同观测源对云团发展、垂直积分液态水和地面降水之间的互补信息。

### 5.5 物理机制回归

NowcastNet 代表了“深度学习 + 物理约束/物理先验”的方向。相比纯数据驱动模型，物理机制可以帮助模型在极端降水、对流快速增长和外推外样本场景中保持合理性。后续可重点关注：质量守恒、平流约束、光流运动场、降水增长消散机制、流体动力学约束等。

## 6. 针对本课题的建议阅读顺序

1. **基础与问题定义**：ConvLSTM；PredRNN/MIM/SimVP 作为时空预测背景。
2. **四个目标数据集统一基准**：DiffCast、AlphaPre、DuoCast。
3. **生成式路线**：PreDiff -> DiffCast -> CasCast -> DuoCast -> FlowCast。
4. **强降水和高频细节**：FACL、AlphaPre、WADEPre。
5. **多源融合**：MS-FTNet、SEVIR 相关多源模型。
6. **物理机制**：NowcastNet、RAFT optical flow、后续 physics-informed nowcasting。

## 7. 后续可继续追踪的问题

- **数据集一致性**：DiffCast、AlphaPre、DuoCast 都声称覆盖 SEVIR、MeteoNet、Shanghai、CIKM，但不同论文可能使用不同裁剪、归一化、时间步长和评价阈值。若要复现实验，需要先统一数据预处理。
- **指标选择**：除 MAE/MSE 外，应重点关注 CSI、POD、FAR、HSS、CRPS、FSS，以及不同降水强度阈值下的表现。
- **极端降水**：Shanghai 和 CIKM 可能更适合讨论强对流局地事件；SEVIR 更适合讨论多源融合与 VIL 预测。
- **概率预报**：生成式模型需要评估多样性、可靠性和校准，不应只看单样本误差。
- **业务可用性**：扩散模型采样成本高，FlowCast、级联式方法和轻量 3D/2D 编码结构值得关注。

## 8. 参考链接

- DiffCast, CVPR 2024: <https://openaccess.thecvf.com/content/CVPR2024/html/Yu_DiffCast_A_Unified_Framework_via_Residual_Diffusion_for_Precipitation_Nowcasting_CVPR_2024_paper.html>
- AlphaPre, CVPR 2025: <https://openaccess.thecvf.com/content/CVPR2025/html/Lin_AlphaPre_Amplitude-Phase_Disentanglement_Model_for_Precipitation_Nowcasting_CVPR_2025_paper.html>
- DuoCast, AAAI 2026: <https://ojs.aaai.org/index.php/AAAI/article/view/41294>
- DuoCast, arXiv 版本: <https://arxiv.org/abs/2412.01091>
- WADEPre, arXiv 2026: <https://arxiv.org/abs/2602.02096>
- CasCast, ICML 2024: <https://proceedings.mlr.press/v235/nie24a.html>
- PreDiff, NeurIPS 2023: <https://proceedings.neurips.cc/paper_files/paper/2023/hash/f86a2e5b9f75b07c5b38c8f9f3c81a4b-Abstract-Conference.html>
- FACL, NeurIPS 2024: <https://openreview.net/forum?id=CgaN1eqZdn>
- FlowCast, ICLR 2026: <https://openreview.net/forum?id=7rPqx2ERWg>
- MS-FTNet, Sensors 2026: <https://www.mdpi.com/1424-8220/26/9/2868>
- NowcastNet, Nature 2023: <https://www.nature.com/articles/s41586-023-06184-4>
- Global MetNet, Nature 2025: <https://www.nature.com/articles/s41586-025-08897-0>
- EGU 2026 spatiotemporal encoding nowcasting abstract: <https://doi.org/10.5194/egusphere-egu26-20598>
- EGU 2026 RAFT weather radar nowcasting abstract: <https://doi.org/10.5194/egusphere-egu26-7614>
