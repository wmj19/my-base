# PINN backlog

最后更新：2026-06-04

## 待精读

- 精读 Raissi et al. 2019，整理基本公式、损失项、正/反问题例子和代码入口。
- 精读 Wang et al. 2021 和 Wang et al. 2022，整理经典 PINN 训练失败的主要原因。
- 对比 Krishnapriyan et al. 2021 的失败案例与 Raissi et al. 2019 的成功案例，形成“什么时候不该直接用 PINN”的判断清单。
- 跑一次 DeepXDE 官方示例，记录环境、问题类型、收敛曲线和常见报错。

## 待补充方向

- hard constraints / trial solution 构造：边界条件强满足与软惩罚的差异。
- 自适应采样：residual-based adaptive refinement、RAR、重要性采样。
- 损失平衡：GradNorm、NTK weighting、learning-rate annealing、自适应权重。
- 可扩展性：XPINN、cPINN、FBPINN、domain decomposition、parallel PINN。
- 与 neural operator 的关系：DeepONet、FNO、PINO，区分“解单个 PDE 实例”和“学习解算子”。
- 应用专题：流体、热传导、材料、地下水/水文、气象降水物理约束。

## 待核验

- 各论文的最新开源代码状态、维护活跃度和许可证。
- 近两年 PINN 综述是否已有更系统的 benchmark 或 negative results。
- PINN 在实际工程中的成功案例是否能证明相对传统数值方法的成本优势。
