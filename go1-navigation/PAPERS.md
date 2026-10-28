# Go1 语义导航与动态避障｜重点文献与官方源码目录

> 核对日期：2026-10-28。发表级别以期刊/会议官方或论文项目页为准；开源仓库存在 ≠ 训练全流程可直接复现；所有源码版本、依赖及授权须在实际安装前二次确认。

## 核心论文（先按任务，再按复现难度排序）

| 优先级 | 论文 / 来源 | 主要用途 | 论文与作者源码 | 复现风险 |
|---|---|---|---|---|
| S | **ApexNav** — IEEE RA-L 2025 / ICRA 2026 | 零样本找物体、目标记忆、探索策略 | [论文](https://arxiv.org/abs/2504.14478) · [代码](https://github.com/Robotics-STAR-Lab/ApexNav) | 有评测/现实代码及 ROS2 分支，仍需核对 HM3D/依赖 |
| S | **VLFM** — ICRA 2024 | RGB-D 与语言价值地图、前沿探索 | [论文](https://doi.org/10.1109/ICRA57147.2024.10610712) · [代码](https://github.com/rai-opensource/vlfm) | 使用较旧 PyTorch/Habitat；RTX 5090 不能照搬环境 |
| S | **ComposableNav** — CoRL 2025 | 行人动态交互、轨迹扩散规划、RL | [论文](https://proceedings.mlr.press/v305/hu25c.html) · [代码](https://github.com/ut-amrl/ComposableNav) | 有数据生成/预训练/RL 脚本；旧 Torch + CUDA 需迁移，须验证实际 demo |
| S | **NaVILA** — RSS 2025 | 四足 VLA 高层命令 + 底层运动策略 | [主页](https://navila-bot.github.io/) · [VLA 代码](https://github.com/AnjieCheng/NaVILA) · [低层代码](https://github.com/yang-zj1026/legged-loco) · [基准](https://github.com/yang-zj1026/NaVILA-Bench) | 对象以 Go2/G1 等为主，不是 Go1 即插即用；Habitat/VLN-CE 环境较旧 |
| A | **VLN-Game** — IEEE T-RO 2026 | 目标属性、语言描述与 3D 语义搜索 | [论文](https://doi.org/10.1109/TRO.2026.3677047) · [代码](https://github.com/ybgdgh/VLN-Game) | HM3D、Open3D/渲染与较旧依赖适配 |
| A | **VLMaps** — ICRA 2023 | 地点和空间关系的视觉语言地图 | [论文](https://arxiv.org/abs/2210.05714) · [代码](https://github.com/vlmaps/vlmaps) | 原作者已披露导航角落碰撞等局限，不是安全避障模块 |
| B | **MobileVLA-R1 2.0** — 2026 预印本 | 与现有学习项目衔接：reasoning、action 与 RL | [论文](https://arxiv.org/abs/2609.06251) · [代码](https://github.com/AIGeeksGroup/MobileVLA-R1-2.0) | 不将前向验证当闭环成功或完整训练复现 |
| B | **TIC-VLA** — 2026 | 异步语义推理、实时控制延迟 | [论文](https://arxiv.org/abs/2602.02459) · [代码](https://github.com/ucla-mobility/TIC-VLA) | 实时延迟模型/数据不同，需先核对任务定义 |

## 相关基础论文

- [Grounding DINO: Marrying DINO with Grounded Pre-Training for Open-Set Object Detection](https://arxiv.org/abs/2303.05499) — 开放词汇目标检测。[代码](https://github.com/IDEA-Research/GroundingDINO)
- [ByteTrack: Multi-Object Tracking by Associating Every Detection Box](https://arxiv.org/abs/2110.06864) — 行人跨帧跟踪。[代码](https://github.com/ifzhang/ByteTrack)
- [Social GAN: Socially Acceptable Trajectories with Generative Adversarial Networks](https://arxiv.org/abs/1803.10892) — 人群轨迹预测基础；不是 Go1 控制器。
- [Nav2 Collision Monitor 官方文档](https://docs.nav2.org/rolling/configuration_and_development/configuration_guide/core_servers/collision_monitor/) — 工程安全监控；它仍不能替代真机安全评估。

## 阅读优先顺序

1. **W01–W06**：沿用现有 [8 周阅读安排](../reading-2026/ROADMAP-8-WEEKS.md)，重点建立 VLA/实时/高层低层接口认识。
2. **W07–W08**：ApexNav + VLFM → VLN-Game；将物体搜索作为第一个仿真闭环。
3. **W09–W12**：ComposableNav + NaVILA + ByteTrack + Grounding DINO；先实现可验证的动态避障，再研究学习式规划。
4. **W13–W16**：VLMaps + VLN-Game + NaVILA；完成物体/人/地点任务统一接口和消融报告。

每篇精读前先写 **3 阶段 6 节学习大纲**（概要、相关工作、结构、公式、实验、源码复现）；不因“顶会”而默认可直接运行。

## 复现前必须核实

- 论文发表/预印本版本是否对应源码 commit。
- 官方权重是否存在、许可是否允许研究使用；数据集 HM3D 的授权、scene 与任务 episode 是否匹配。
- PyTorch/CUDA 是否支持 RTX 5090（Blackwell）；旧版 Habitat 的 ABI、EGL、编译链问题是否已解决。
- 使用哪一种仿真器、哪一种机器人模型，以及动作接口是否匹配；仿真器更换后不可宣称同协议复现。
- 测试中目标人是否被明确标识并获授权；普通人检测、目标重识别与人脸身份识别必须区分。
