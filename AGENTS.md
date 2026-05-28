# AGENTS.md

这个仓库是一个个人知识库的原始目录。当前内容很少，但后续会持续增加专题目录、论文笔记、资料索引、阶段性总结和可能的实验记录。任何自动化助手进入本仓库后，应先阅读本文件，再进行检索、整理或写作。

## 总体原则

- 先理解已有目录和命名，再新增内容；不要为了“看起来整齐”随意移动或重命名用户已有文件。
- 知识库以 Markdown 为主，优先使用可被普通文本工具检索、diff 和长期维护的格式。
- 新增资料时要留下来源、日期和判断依据。论文、榜单、顶会顶刊信息、模型名称、数据集状态等可能变化的信息，需要联网核验后再写入。
- 保持目录小而清晰。一个文件只承担一个主要职责：索引、论文笔记、方法综述、数据集说明、阅读计划等不要混在一起。
- 对不确定的信息明确标注“不确定”“待核验”或“推测”，不要把推断写成事实。
- 优先增量更新已有索引和总结，而不是重复创建相近文件。

## 建议的知识库形态

根目录只放跨专题的说明、全局索引和通用模板。每个专题目录内部维护自己的入口文件、资料库、笔记和阶段性总结。

建议结构：

```text
.
├── AGENTS.md
├── README.md
├── templates/
│   ├── paper-note.md
│   └── survey-note.md
└── QPE/
    ├── README.md
    ├── index.md
    ├── reading-list.md
    ├── papers/
    ├── surveys/
    ├── methods/
    ├── datasets/
    ├── venues/
    ├── figures/
    └── backlog.md
```

如果这些目录暂时不存在，不要一次性创建空目录；在第一次确实需要写入对应内容时再创建。

## QPE 专题定位

`QPE/` 用来调研“定量降水估计的顶刊论文，重点关注利用深度学习方法实现定量降水估计”。这里的 QPE 指 Quantitative Precipitation Estimation。

整理时应重点关注：

- 研究任务：雷达回波到降水率/降水量估计、卫星/多源融合 QPE、时空外推辅助 QPE、极端降水估计等。
- 方法类别：CNN、U-Net、ConvLSTM、Transformer、扩散模型、物理约束模型、多模态融合、概率估计和不确定性建模等。
- 数据来源：天气雷达、卫星、雨量站、再分析资料、地形等辅助变量。
- 评估指标：RMSE、MAE、Bias、Correlation、CSI、POD、FAR、HSS、NSE、分雨强等级指标等。
- 顶刊与高质量来源：Remote Sensing of Environment、IEEE TGRS、Water Resources Research、JHM、JGR Atmospheres、Atmospheric Research、HESS、npj Climate and Atmospheric Science、Nature 系列相关期刊等。具体期刊分区和论文影响力需要逐条核验。

## QPE 目录建议

`QPE/README.md` 作为专题入口，说明研究范围、当前问题、维护方式和最新进展。

`QPE/index.md` 作为人工可读索引，按主题链接到论文笔记、方法总结、数据集说明和阶段性综述。

`QPE/reading-list.md` 维护候选论文列表，建议用表格记录：

- 标题
- 年份
- 期刊/会议
- 方法类型
- 数据源
- 区域/数据集
- 是否深度学习
- 是否顶刊或高影响来源
- 当前状态：待筛选、待读、已读、已总结、排除
- 链接或 DOI

`QPE/papers/` 存放单篇论文笔记。建议文件名：

```text
YYYY-first-author-short-title.md
```

例如：

```text
2024-zhang-transformer-qpe.md
```

`QPE/surveys/` 存放阶段性综述，例如“深度学习 QPE 方法谱系”“雷达 QPE 顶刊论文脉络”“数据集与评估指标对比”。

`QPE/methods/` 存放方法专题卡片，例如 `unet.md`、`transformer.md`、`physics-informed.md`。

`QPE/datasets/` 存放数据集和观测源说明，例如雷达产品、雨量站网络、卫星降水产品、多源融合产品。

`QPE/venues/` 存放期刊、会议、论文筛选标准和顶刊判断依据。

`QPE/backlog.md` 记录待查问题、待读论文、待核验来源和下一步计划。

## 论文笔记格式

单篇论文笔记建议包含以下固定小节：

```markdown
# Title

- Year:
- Venue:
- DOI / URL:
- Authors:
- Task:
- Data:
- Method:
- Metrics:
- Code / Dataset:
- Status:
- Tags:

## 一句话总结

## 研究问题

## 方法要点

## 数据与实验设置

## 结果与指标

## 创新点

## 局限性

## 和本知识库主题的关系

## 可复用信息

## 待核验
```

写论文笔记时，摘要、实验结论和贡献应尽量用自己的话概括。需要引用原文时只引用短句，并附来源链接。

## 更新流程

新增 QPE 论文时建议按以下顺序：

1. 在 `QPE/reading-list.md` 增加候选条目。
2. 核验 DOI、期刊、年份、任务和是否使用深度学习。
3. 在 `QPE/papers/` 创建单篇论文笔记。
4. 更新相关方法、数据集或期刊索引。
5. 如果论文改变了已有判断，更新 `QPE/surveys/` 中的阶段性综述。
6. 把遗留问题写入 `QPE/backlog.md`。

做阶段性整理时，优先输出“可继续迭代”的文件，而不是一次性长文：

- `QPE/surveys/YYYY-MM-topic.md`
- `QPE/methods/topic.md`
- `QPE/datasets/source-name.md`
- `QPE/venues/journal-screening.md`

## 命名与写作约定

- 文件名使用小写英文、数字和连字符，避免空格。
- 中文内容可以保留中文标题和正文；元数据字段尽量保持英文，方便后续脚本解析。
- 日期使用 `YYYY-MM-DD`。
- 同一主题不要重复创建多个近似文件；先搜索再新增。
- 大文件、PDF、图片和数据不要直接堆进根目录。图片放入专题下的 `figures/`，原始大文件应先询问用户是否需要纳入仓库。

## 给自动化助手的操作规则

- 开始任何任务前，先运行快速目录检查，例如 `rg --files` 或 `find`，确认当前结构。
- 研究最新论文、期刊信息、引用情况或工具状态时必须联网核验，并在结果中给出来源链接。
- 对知识库进行结构性调整前，先说明调整理由和影响范围；不要静默批量迁移文件。
- 新增或修改多个文件后，简要说明改了哪些文件、为什么改、后续建议是什么。
- 不要删除用户资料、笔记、PDF、图片或未跟踪文件，除非用户明确要求。
- 遇到同名、重复或内容冲突文件时，先保留并标注冲突，再让用户决定合并策略。

