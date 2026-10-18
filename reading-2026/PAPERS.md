# 2026 年及以后：MobileVLA-R1 2.0 相关论文目录

**核对日期：2026-10-18。** 以下“日期”优先使用 arXiv 首次提交年月。预印本不自动等于会议录用；若有正式发表信息，另行标明来源。开源状态会变化，学习时请复查。

## 核心 7 篇（均为 2026 年）

| 优先 | 论文（首次发表时间） | 研究焦点 | 论文 | 开源/项目状态 |
|---|---|---|---|---|
| S | **MobileVLA-R1 2.0: RL-Enhanced Reasoning for Mobile Robot Control**（2026-09） | 多粒度 CoT、GRPO、reasoning-conditioned action decoder；**课程锚点** | [arXiv:2609.06251](https://arxiv.org/abs/2609.06251) | [作者 GitHub](https://github.com/AIGeeksGroup/MobileVLA-R1-2.0)；其 README 声明实现未跑通官方基准，不能将示例当已验证结果 |
| S | **GroundingVLN: Reasoning and Acting with Grounding for Vision-Language Navigation**（2026-09） | 用视觉定位把语言推理连到像素目标，GEAR 强化学习与几何规划 | [arXiv:2609.18581](https://arxiv.org/abs/2609.18581) | [GitHub](https://github.com/Teacher-Tom/GroundingVLN) 存在，但核对时内容非常少；完整代码/权重发布须确认 |
| S | **RoboAlign: Learning Test-Time Reasoning for Language-Action Alignment in Vision-Language-Action Models**（2026-03） | 强化学习细化 reasoning 到动作的对齐，比较 diffusion action head | [arXiv:2603.21341](https://arxiv.org/abs/2603.21341) | 未核实完整官方训练仓库，勿假设可复现 |
| A | **TIC-VLA: A Think-in-Control Vision-Language-Action Model for Robot Navigation in Dynamic Environments**（2026-02） | 显式建模 VLM 推理延迟、异步语义与即时控制 | [arXiv:2602.02459](https://arxiv.org/abs/2602.02459) | [作者 GitHub](https://github.com/ucla-mobility/TIC-VLA) 有训练和评测代码；仓库标注 ICML 2026 |
| A | **Uni-LaViRA: Language-Vision-Robot Actions Translation for Unified Embodied Navigation**（2026-05） | training-free LA/VA，语义指令和像素目标跨机器人任务转换；Go1 关联强 | [arXiv:2605.27582](https://arxiv.org/abs/2605.27582) | [作者关联仓库](https://github.com/NJU-R-L-Group-Embodied-Lab/uni-lavira-code) 有 `real-world-code/unitree_go1/`，但 README TODO 未确认所有部署功能已完整验证 |
| A | **Foresight: Iterative Reasoning About Clues that Matter for Navigation**（2026-06） | VLM 提议路径 → 批判 → 迭代修正；人类偏好奖励模型/RL | [arXiv:2606.12550](https://arxiv.org/abs/2606.12550) | [作者 GitHub](https://github.com/ut-amrl/foresight_public) 有代码和 `legged_deployment/`，不代表已适配 Go1 |
| B | **DA-Nav: Direction-Aware City-Scale Vision-Language Navigation**（2026-07） | 方向指令、偏航识别、目标网格与长距离导航恢复 | [arXiv:2607.11638](https://arxiv.org/abs/2607.11638) | 尚未独立确认对应官方完整代码仓库；先论文学习 |

## 与 MobileVLA 2.0 的对照问题

| 论文 | 与 2.0 共性 | 需要重点看出的不同 |
|---|---|---|
| GroundingVLN | CoT + RL + 可执行导航决策 | 以像素级 grounding / spatial goal 接几何规划，不是仅从隐藏状态直接回归速度 |
| RoboAlign | SFT 后利用 RL 让动作与推理更一致 | 测试时 reasoning、动作 token 和 diffusion head 的接口、奖励来源不同 |
| TIC-VLA | 语言视觉推理用于运动 | 延迟语义/异步控制与训练延迟扰动，比离线 token 损失更靠近真实时序问题 |
| Uni-LaViRA | 视觉语言到行动 | 尽量不额外训练，通过语言/视觉目标到机器人动作的分层转换 |
| Foresight | 目标相关推理与 RL | 在行动前迭代提出/批判规划，与 2.0 的 GRPO 候选比较不是同一种闭环 |
| DA-Nav | CoT 辅助长程导航 | 关注偏航恢复和方向引导，强调城市尺度与轨迹纠错 |

## 阅读产出要求

每篇至少产出：
- 一句话研究问题 + 系统输入/输出
- 结构图（自己重画）和关键变量/损失
- 论文作者的实验设置、基线、至少 2 项关键指标（**明确标“作者报告”**）
- 与 MobileVLA 2.0 的对照表（可训练参数、推理时序、动作表示、底层控制）
- 代码情况：已核实的路径/文件、运行依赖和缺失文件；**找不到写“未核实”**
- 一个可验证的小实验或最小复现目标（不可运行则给出原因）

## 背景材料（不算进 2026+ 核心目录）

- [MobileVLA-R1（首次预印本 2025，项目注明 ECCV 2026）](https://arxiv.org/abs/2511.17889)：用于理解 R1 → R1 2.0 的变化。
- [NaVILA（2024）](https://arxiv.org/abs/2412.04453)：用于理解四足高层导航与底层运动接口。

**维护规则：** 后续每新增 2026+ 论文，先核对 arXiv 标识、首次提交时间、正式发表状态、项目与代码链接，再决定是否进入主阅读表；科研结论必须写证据出处。
