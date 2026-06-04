# 缺失模态多模态学习综述笔记

整理日期：2026-06-04  
论文：A Comprehensive Survey on Deep Multimodal Learning with Missing Modality  
作者：Renjie Wu, Hu Wang, Hsiang-Ting Chen  
版本：arXiv:2409.07825v1，2024-09-12  
来源：本地 PDF `../../papers/2409.07825v1.pdf`  
状态：已阅读本地 PDF，未联网核验发表状态、代码和后续版本。  
标签：multimodal learning; missing modality; robust fusion; remote sensing; multi-source nowcasting

## 1. 为什么放在这个专题里

这篇不是降水临近预报论文，也没有针对雷达回波 nowcasting 做实验，因此不应放入 `literature/paper-index.json` 的 nowcasting 文献索引。

它适合作为多源融合方法论参考：降水临近预报常见输入包括雷达、卫星、雨量计、地面站、GNSS、NWP 或基础模型先验。真实业务中这些源经常因为传感器故障、云遮挡、延迟、站点稀疏、区域覆盖不足或数据权限问题而缺失。论文讨论的 MLMM（Multimodal Learning with Missing Modality）正好对应“多源降水模型在部分输入缺失时如何保持可用”的问题。

## 2. 论文核心问题

传统多模态学习通常假设训练和推理阶段每个样本都包含全部模态。论文把这种设定称为 MLFM（Multimodal Learning with Full Modality）。

MLMM 则考虑训练或推理阶段只有部分模态可用的情况。核心挑战是：模型需要动态处理任意数量的可用模态，并在缺失模态时尽量接近全模态性能。

论文认为缺失模态可能来自：

- 传感器故障或硬件限制。
- 隐私、成本和数据访问约束。
- 数据传输损坏或时空不同步。
- 环境干扰，例如噪声、遮挡、极端条件。
- 某些领域天然难以收集完整模态。

对应到降水临近预报，可以理解为雷达缺测、卫星通道延迟、站点观测不齐、GNSS 站网稀疏、NWP 产品未及时到达等问题。

## 3. 方法分类

论文将深度 MLMM 方法分成四个维度，共 13 类。

### 3.1 Modality Augmentation

这一类在原始模态数据层面处理缺失。

**Modality Composition**  
用零值、随机值、复制历史帧、KNN 检索相似样本等方式补齐缺失模态。优点是简单，可作为 baseline；缺点是像素级任务和高缺失率场景容易引入伪信息，且可能削弱数据多样性。

对 nowcasting 的启发：雷达帧缺失时可以先建立零填充、上一时刻复制、光流外推或相似天气样本检索 baseline，但不要把它当作强方法。

**Modality Generation**  
用 AE、U-Net、GAN、Diffusion 等生成缺失模态的原始数据。可分为每个模态一个生成器的 individual generation，以及一个统一生成器同时生成多模态的 unified generation。

对 nowcasting 的启发：satellite-to-radar、radar proxy、卫星生成雷达 VIL、NWP 生成雷达先验都属于这个思路。但风险是生成器重、训练复杂，并且强回波峰值和极端降水可能被生成模型平滑或幻觉化。

### 3.2 Feature Space Engineering

这一类在特征层面补全、约束或融合。

**Regularization-based / Correlation-driven**  
通过张量秩正则、CCA、最大似然、HSIC 等约束学习跨模态相关性。优点是可以促使不同模态特征对齐；缺点是模态一多，损失权重和约束平衡会变复杂。

对 nowcasting 的启发：雷达、卫星、站点、GNSS 的表征可以用相关性约束对齐，但要避免强行让物理含义不同的模态共享过多信息。

**Representation Composition**  
在特征层面用池化、加和、sign-max、token merging、相似样本特征检索等方式处理任意数量的可用模态。它比原始数据补全更轻量，但容易丢失关键模态信息。

对 nowcasting 的启发：多源模型最好支持“可用哪些源就融合哪些源”，例如 radar-only、radar+satellite、radar+station 都能跑，而不是固定通道数后硬填空值。

**Representation Generation**  
生成缺失模态的中间表示，而不是生成原始图像或时间序列。论文分为两类：

- indirect-to-task：训练时用重建任务辅助学习，推理时丢弃重建头。
- direct-to-task：直接把可用模态映射到缺失模态表征。

对 nowcasting 的启发：相比直接生成一整幅未来卫星图或雷达图，生成缺失源的 latent representation 可能更稳、更轻，也更适合接到现有 U-Net、ConvLSTM、Transformer 或 diffusion backbone。

### 3.3 Architecture Engineering

这一类通过架构设计适配缺失模态。

**Attention-based**  
包括单模态内部注意力和跨模态 masked attention。缺失模态可以被 attention mask 忽略，也可以用 learnable token 或共享 fusion token 表示。优点是可扩展，适合多模态 Transformer；缺点是训练资源大，且缺失的关键模态不一定能被现有模态弥补。

对 nowcasting 的启发：雷达-卫星 cross-attention、站点-雷达 attention、masked modality token 是多源降水模型的自然选择。模型需要显式知道哪些模态缺失，而不是只看到一堆零值。

**Distillation-based**  
用全模态 teacher 指导缺失模态 student，可蒸馏 logits、中间特征，也可用 mean teacher 或 self-distillation。优点是实现相对直接，推理时可以只用可用模态；缺点是多数方法要求训练阶段有全模态样本。

对 nowcasting 的启发：如果历史训练集里雷达、卫星、站点都完整，可以训练 full-source teacher，再蒸馏 radar-only 或 radar+satellite student。若训练集本身也大量缺模态，则普通蒸馏不够。

**Graph learning-based**  
把模态、样本或 patient-modality 关系构造成图，用图融合或 GNN 传播补全信息。优点是能显式建模模态和样本之间的关系；缺点是规模大时效率和实现复杂度较高。

对 nowcasting 的启发：站点网络、雷达站网、多区域相似天气过程可以构造成图，但要警惕大范围高分辨率网格上的计算开销。

**MLLMs**  
多模态大模型可接收任意数量的模态 token，因此天然有一定缺失模态适配能力。不过论文指出缺少专门的缺失模态 benchmark，训练和推理成本也高。

对 nowcasting 的启发：LangPrecip、Omni-Weather 这类统一天气理解/生成模型可以关注，但目前更适合作为研究方向，不宜直接替代高时效业务 nowcasting 模型。

### 3.4 Model Selection

这一类不一定补全模态，而是为不同模态组合选择模型。

**Ensemble**  
为不同模态或不同模型训练专家，再做投票、平均、加权或概率融合。优点是缺失时灵活；缺点是存储和维护成本高，跨模态关系建模弱。

**Dedicated**  
为每一种缺失组合训练专门模型。例如三模态场景最多需要 7 个组合模型。优点是针对性强；缺点是模态数量增加后组合爆炸。

**Discrete Scheduler**  
用 LLM 或调度器根据输入模态和任务选择下游模块。优点是灵活；缺点是依赖模块库完整性、调度延迟和指令理解可靠性。

对 nowcasting 的启发：业务系统可以保留 radar-only、radar+satellite、radar+station 等多个轻量模型，再用数据可用性调度，但维护成本必须计入。

## 4. 重要结论

论文有几个对研究设计很有用的结论：

1. 现有方法多数仍偏向“恢复缺失模态”，而不是直接在缺失条件下推理。论文统计的 315 篇工作中，约 75.5% 属于 recovery 路线。
2. 中间特征补全最常见，因为相比原始数据生成，它噪声和偏差更小；相比 late feature，它又保留更多模态特异信息。
3. 很多方法假设训练阶段能访问完整模态，只在测试阶段模拟缺失。这和真实业务数据并不完全一致。
4. 公开数据集中自然缺失模态较少，很多论文只是人工随机丢弃模态做评估。
5. 流式数据、科学领域、多模态强化学习和 MLLM 缺失模态 benchmark 都还不足。

## 5. 对多源降水临近预报的转译

### 5.1 需要区分两种缺失

**推理缺失**  
训练时数据完整，部署时某个输入源偶尔缺失。适合蒸馏、masked attention、modality dropout、ensemble/dedicated model。

**训练缺失**  
历史数据本身不完整，例如早期没有卫星某通道、部分站点长期缺测、不同区域数据源不同。普通 full-modality teacher 和重建监督会失效，需要 representation composition、direct representation generation、graph propagation 或能处理不完整训练集的方法。

### 5.2 推荐实验设置

多源 nowcasting 论文如果要证明鲁棒性，至少应该报告：

- full-source：雷达+卫星+站点/GNSS/NWP 全部可用。
- radar-only：只保留雷达，作为核心业务兜底。
- leave-one-source-out：每次移除一个源，看性能损失。
- random missing：随机缺失不同模态，报告均值和方差。
- severe missing：高缺失率或连续时段缺失，模拟真实传感器故障。
- train-missing vs test-missing：区分训练集本身缺失和仅测试时缺失。

指标上不应只看 MSE/MAE，还要看高阈值 CSI/POD/FAR/HSS、可靠性、推理延迟，以及强降水案例图。

### 5.3 方法选型建议

如果只想建立强 baseline：  
从 modality dropout + missing mask + attention fusion 开始，保证模型知道哪些源缺失。

如果关注实时业务：  
优先考虑 representation composition、轻量 adapter、蒸馏到 radar-only/radar+satellite student，而不是重型原始模态生成。

如果关注雷达覆盖弱区域：  
可以研究 modality generation 或 satellite-to-radar，但必须评估生成雷达代理是否产生不可靠强回波幻觉。

如果关注站点/GNSS 稀疏性：  
graph learning 和不规则空间 token 可能比简单网格拼接更合适。

如果关注统一多任务天气模型：  
MLLM/基础模型路线值得跟踪，但需要专门的缺失模态 benchmark，否则很难说明它真的鲁棒。

## 6. 可直接写进后续论文的问题

1. 多源降水临近预报常声称融合有效，但较少系统研究输入源缺失时的鲁棒性。
2. 多数模型把缺失源填零或直接丢弃，容易把“缺失”与“真实零值/弱信号”混淆。
3. 需要同时评估 train-time missing 和 test-time missing，因为业务历史数据和实时数据都会不完整。
4. 对强降水来说，缺失的可能正是关键先兆模态，例如卫星水汽通道或 GNSS PWV；简单 non-recovery 方法可能不够。
5. 生成式补全有潜力，但在强回波和极端降水下要特别检查可靠性和虚假结构。

## 7. 待核验

- arXiv 后续版本是否更新了 taxonomy、统计数字或引用列表。
- 是否已有正式发表版本。
- 是否有作者维护的论文列表、代码或数据表。
- MLMM 中是否已有气象、雷达、卫星或地球系统科学的专门 benchmark。

## 8. 参考入口

- 本地 PDF：`../../papers/2409.07825v1.pdf`
- 辅助阅读 deck：`../../decks/2409-07825-missing-modality-deck.html`
- 相关专题总结：[多源融合降水临近预报方法整理](multi-source-fusion-methods.md)
