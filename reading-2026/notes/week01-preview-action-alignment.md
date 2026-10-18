# W01 · 先前预习资料：动作奖励与语言对齐

- 日期：2026-10-18（正式第1周开始前预习）
- 学习状态：**先修补充资料，包含后续章节所需知识；非正式第1节学习验收**
- 本周论文：[MobileVLA-R1 2.0](https://arxiv.org/abs/2609.06251) / [RoboAlign](https://arxiv.org/abs/2603.21341)
- 本周总任务：[8周计划](../ROADMAP-8-WEEKS.md)；[进度表](../PROGRESS.md)
- 本笔记来源：两篇原论文、[MobileVLA-R1 2.0 GitHub README](https://github.com/AIGeeksGroup/MobileVLA-R1-2.0)。以下公式/方法基于原论文总结，示例数值为教学演算，不是作者实验结果。

## 问题：理解正确为什么不代表动作正确？

MLLM 可用语言说出“向杯子靠近并闭合夹爪”，但仅由语言答案无法确定夹爪位置、姿态和正确时序。两篇都尝试让**推理质量受动作结果约束**，不是只优化文本看起来有多合理。

## 两篇方法概览

### MobileVLA-R1 2.0（导航 / 四足运动与移动操作）

```mermaid
flowchart LR
 V[RGB / 深度 / 点云 + 指令] --> F[编码和投影]
 F --> R[NaVILA / LoRA 推理]
 R --> D[Reasoning-Conditioned Action Decoder]
 F --> D
 D --> A[平面速度 Vx Vy ω + 行为类别]
 A --> C[机器人专用控制器]
```

训练是 Long SFT → Action SFT（训练动作解码器）→ 离线 GRPO（动作解码器保持冻结，更新语言主干 LoRA）。GRPO 评价移动速度一致性、行为标签匹配和格式，**必须有可信动作监督**。当前作者 GitHub README 特别注明其发布代码尚未完成实际训练/推理/机器人基准验证。

### RoboAlign（操作机器人 / 具身语言模型转 VLA）

```mermaid
flowchart LR
 O[图像 + 指令 + 动作示范] --> S[SFT: 推理 + FAST 动作 token]
 S --> G[GRPO: 奖励更精确的动作 token]
 G --> M[对齐的 MLLM 主干冻结]
 M --> H[训练新 Diffusion Action Head]
 H --> A[连续机器人动作轨迹]
```

SFT 混合通用/具身理解、机器人 VQA、推理数据、FAST 动作 token 等；GRPO 以后在相应下游数据集上冻结主干并训练新扩散动作头，检验语言/动作对齐是否真正改善策略。

## 关键原理 1：RoboAlign 的 FAST token

对机器人一段长度为 H 的动作轨迹 `a_{1:H}`，FAST 通过 DCT 系数与量化/BPE，将连续动作块变成可生成的离散 token 序列。它是**编码/训练表示**，不意味着机器人直接执行字符串。

## 关键原理 2：RoboAlign 如何打分

来源：论文 §4.2，式 (2)。动作准确奖励是“从第一个 token 开始连续匹配的前缀长度 / 标注动作 token 总长度”，记为

`r_a = LCP(T_generated, T_target) / len(T_target)`

另有格式奖励 `r_f∈{0,1}`，总奖励 `r=(r_f+r_a)/2`。

**教学演算（非论文实测）：** 目标 token = [A, B, C, D]；生成 token = [A, B, X, D]；最长正确前缀 [A,B] 长 2，因此 `r_a=2/4=0.5`。如果格式合规 `r_f=1`，总奖励 `r=0.75`。即使最后 token D 恰好相同，前面第三个 token 不同仍不能跳过去计入前缀奖励。

## 关键原理 3：GRPO

同一输入采样多个推理+动作候选；每个候选分配奖励 `r_i`，再按同组均值与标准差归一化优势：

`A_i = (r_i - mean(r)) / std(r)`

提高组内高质量候选的生成概率，并通过裁剪/KL 正则防止策略更新失控。实际代码还需处理 `std=0` 等数值情况。

## 核心对比表

| 维度 | MobileVLA-R1 2.0 | RoboAlign |
|---|---|---|
| 主任务 | 移动机器人导航、四足/移动操作 | 操作机器人任务（LIBERO、CALVIN、真实任务） |
| 对齐监督 | 速度、行为、格式奖励 | FAST 动作 token 前缀匹配 + 格式奖励 |
| 动作表示 | 解码器输出 [Vx,Vy,ω] + behavior | 对齐阶段 FAST token；VLA 阶段扩散动作头输出连续动作 |
| GRPO 发生在 | Action SFT 后，冻结动作解码器 | MLLM 的 SFT 后；随后冻结 MLLM 另训练动作头 |
| 可迁移到 Go1 的价值 | 任务层速度和低层控制分离 | “推理必须与动作正确性相关”的训练思想；动作空间不可直接照搬 |

## 本节可回答的问题

1. 为什么提高 VQA 答题分数，不能等同于提高机器人实际成功率？
2. 为什么目标 [A,B,C,D] 与预测 [A,B,X,D] 的前缀奖励是 0.5，而不是 0.75？
3. 为什么 RoboAlign 的 FAST token 和 MobileVLA-R1 2.0 的三维速度目标不是同一种动作接口？
4. 两篇都用 GRPO，所评判的动作质量为何不同？

## 实验与阅读边界

- 作者 RoboAlign 论文表 2（LIBERO）报告基线 73.9%、RoboAlign 86.8% 平均成功率；这**不是本地结果**，复述时要附具体对照设置。
- 原论文中相对基线的 17.5% 是**相对提升**，不是“增加 17.5 个百分点”。
- 目前尚未对 RoboAlign 在自己的服务器进行代码或训练复现；不在此文件标为成功。
- [下一步](../ROADMAP-8-WEEKS.md)：读 RoboAlign §4.1–§4.2、MobileVLA 2.0 的 Action SFT/GRPO，比较两种 reward 在错误动作情况下如何打分。

参考：[RoboAlign §4.1–§4.2](https://arxiv.org/html/2603.21341#S4) · [MobileVLA-R1 2.0](https://arxiv.org/abs/2609.06251)。
