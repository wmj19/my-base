# PINN

本目录用于整理 Physics-Informed Neural Networks（PINN，物理信息/物理约束神经网络）相关论文、阅读路线、工具和后续问题。

当前范围：

- 核心概念：用神经网络近似微分方程解，并把 PDE/ODE 残差、初边值条件、观测数据等写入训练损失。
- 任务类型：正问题求解、反问题参数识别、稀疏数据下的物理规律发现、守恒律和复杂区域问题。
- 阅读重点：经典框架、综述、训练失败机制、损失平衡/优化病理、区域分解、工具库和可复现实验。
- 边界说明：neural operator、DeepONet、FNO 等与 PINN 关系密切，但不直接等同于经典 PINN；后续可作为相邻方向单独扩展。

## 目录说明

- [index.md](index.md)：阅读入口、推荐顺序和阶段性判断。
- [literature/paper-index.json](literature/paper-index.json)：结构化论文索引。
- [backlog.md](backlog.md)：后续待查、待精读和待复现实验。

## 当前结论

PINN 的核心价值不只是“用神经网络解 PDE”，而是在数据稀缺、边界/物理约束明确、反问题或参数识别困难时，把已知物理作为可微约束嵌入学习过程。经典 PINN 在简单基准上很优雅，但在多尺度、刚性、强对流/冲击、长时间积分和复杂几何上容易出现优化困难、损失项不平衡和物理解不可靠等问题。

建议阅读时同时看两条线：

- 正向理解线：Raissi et al. 2019 -> Karniadakis et al. 2021 -> Cuomo et al. 2022。
- 批判和改进线：Wang et al. 2021 -> Wang et al. 2022 -> Krishnapriyan et al. 2021 -> cPINN/XPINN/DeepXDE。

元信息最后联网核验：2026-06-04。
