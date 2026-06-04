# PINN reading index

最后更新：2026-06-04

## 入口

- 结构化论文索引：[literature/paper-index.json](literature/paper-index.json)
- 后续待办：[backlog.md](backlog.md)

## 推荐阅读顺序

### 1. 先建立基本框架

1. Raissi, Perdikaris, Karniadakis, 2019, Journal of Computational Physics  
   PINN 的标志性论文，给出正问题、反问题、连续时间和离散时间表述。先读它能快速抓住 PINN 的基本损失结构：数据项、PDE 残差项、初边值条件项。

2. Karniadakis et al., 2021, Nature Reviews Physics  
   更大的 Physics-informed machine learning 综述。适合把 PINN 放到科学机器学习、算子学习、多保真建模、物理先验和不确定性的大图景里。

3. Cuomo et al., 2022, Journal of Scientific Computing  
   PINN 专门综述，适合补齐应用领域、组件选择、工具链、收敛性与未来问题。

### 2. 再看为什么经典 PINN 会失败

4. Wang, Teng, Perdikaris, 2021, SIAM Journal on Scientific Computing  
   重点看梯度流病理、损失项尺度不平衡、刚性训练动力学，以及自适应损失权重/架构改进。

5. Wang, Yu, Perdikaris, 2022, Journal of Computational Physics  
   从神经切线核（NTK）角度解释 PINN 为什么训练困难，适合理解不同损失项收敛速度不一致的问题。

6. Krishnapriyan et al., 2021, NeurIPS  
   很适合当“反宣传”阅读：展示 PINN 在稍复杂问题上可能学到不相关或不物理的解，提醒不要只看 toy examples。

### 3. 最后看扩展和工程工具

7. Lu et al., 2021, SIAM Review, DeepXDE  
   工具库论文，适合开始做小实验。读法上可以边看论文边跑 Burgers、Poisson、反问题等示例。

8. Jagtap, Kharazmi, Karniadakis, 2020, CMAME, cPINN  
   面向守恒律和离散子区域的保守 PINN。适合关注流体、守恒方程、冲击波或降水/输运类问题时阅读。

9. Jagtap, Kharazmi, Karniadakis, 2020, Communications in Computational Physics, XPINN  
   广义时空区域分解 PINN。适合进一步理解复杂几何、长时间域、多尺度问题如何拆分。

10. Pang, Lu, Karniadakis, 2019, SIAM Journal on Scientific Computing, fPINN  
    分数阶 PDE 的 PINN 扩展。不是每个方向都必读，但能帮助理解 PINN 如何和非整数阶微分算子结合。

11. Yang, Meng, Karniadakis, 2021, Nature Communications  
    稀疏数据下发现控制方程。更偏物理信息学习和 PDE discovery，适合作为 PINN 反问题/科学发现路线的高质量案例。

## 初读时建议关注的问题

- PINN 里的“physics-informed”到底是软约束、硬约束，还是架构先验？
- PDE 残差、边界条件、观测数据三类损失项如何配权？
- 自动微分得到的高阶导数是否稳定？对激活函数、归一化、采样点有什么要求？
- PINN 在哪些问题上比传统数值方法有真实优势？哪些场景只是更慢、更难调的替代品？
- 反问题中，PINN 是在估计参数、补全状态，还是同时学习未知物理项？
- 对多尺度、刚性、强间断、冲击波、长时间积分，论文是否给出可靠消融和失败案例？

## 与已有专题的关系

仓库中已有降水 nowcasting 和 QPE 方向材料，里面也出现了 physics-informed / physical constraints。这里的 `pinn/` 先维护通用 PINN 方法论；如果后续研究降水物理约束模型，可以从本目录链接到 `NowcatingPrecipitation/` 或 `QPE/`，避免复制同一份主笔记。
