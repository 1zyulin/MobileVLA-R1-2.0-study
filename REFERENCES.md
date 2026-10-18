# 参考资料与版本

本笔记以一手来源和本次实际运行回执为依据。网页后续可能变化，因此模型、数据和已核查的代码尽量记录固定版本。这里的源码提交是本次查阅依据，不额外声称服务器压缩包已经与该提交逐文件一致。

## 论文和官方项目

1. Huang 等，**MobileVLA-R1 2.0: RL-Enhanced Reasoning for Mobile Robot Control**，2026。[摘要](https://arxiv.org/abs/2609.06251) · [HTML v1](https://arxiv.org/html/2609.06251v1) · [项目主页](https://aigeeksgroup.github.io/MobileVLA-R1-2.0/)。论文中的实验成绩属于作者报告，不能作为本次复现的成绩。
2. [MobileVLA-R1 2.0 官方源码](https://github.com/AIGeeksGroup/MobileVLA-R1-2.0)。已查阅版本 `eb254bdaf2c378454352bfa0e82a15a061de03b7`：[README](https://github.com/AIGeeksGroup/MobileVLA-R1-2.0/blob/eb254bdaf2c378454352bfa0e82a15a061de03b7/README.md)、[数据规范](https://github.com/AIGeeksGroup/MobileVLA-R1-2.0/blob/eb254bdaf2c378454352bfa0e82a15a061de03b7/docs/data.md)。该版本 README 明示未完成训练、推理、机器人运行及基准复现；示例配置不能自动视为已验证配方。
3. [原版 MobileVLA-R1](https://github.com/AIGeeksGroup/MobileVLA-R1)。与 2.0 分开理解；旧版训练子集、补丁或结果不直接移植成 2.0 的已知事实。

## 底座与公开数据

| 资源 | 本次选定版本 | 说明 |
|---|---|---|
| [NaVILA checkpoint](https://huggingface.co/a8cheng/navila-llama3-8b-8f) | `b2294e96581454468d6b94f38201f4f965ef48b7` | 与当前加载布局相符；16 个选定文件下载校验完成 |
| [NaVILA 官方代码](https://github.com/AnjieCheng/NaVILA) | 上游参考 | 底座和权重布局的来源 |
| [MobileVLA-CoT](https://huggingface.co/datasets/AIGeeksGroup/MobileVLA-CoT) | 已保存版本前缀 `1f80df5` | 约 755MB 的选定标注文件，具体计数由本次审计取得；这里只写已留存的短标识，不猜完整提交 |
| [QUARD](https://huggingface.co/datasets/Tong314/QUARD) | `375cbd77d6b8e96ff82e1529f0c44caa5c182d5e` | 17 个任务压缩包及 README，合计 124,804,417,910 字节 |

深度含义的辅助核对参考 QUART-Online 的公开代码：[深度处理](https://github.com/yuan48/QUART-Online/blob/da8a0d3c3c4e8663d414b952c078d29b4316b7c4/walk-these-ways-quart/go1_gym_quart/envs/base/legged_robot.py#L2059-L2082)、[保存 PNG](https://github.com/yuan48/QUART-Online/blob/da8a0d3c3c4e8663d414b952c078d29b4316b7c4/gym_eval_scripts/play_go_isaac_run.py#L771-L789)。所见格式与归一化深度实测相符，但未证明这个源码版本就是归档的生成版本，不能据此补造原始标定或采集参数。

## 本次实验依据

本地原始审计包含模型元数据与文件校验、权重头与空模型对照、真实 GPU 前向、标注解析、命令解析、RGB 缓存与原生校验、CPU 长度审计，以及带时间的后台部署和状态回执。公开文档只保留整理后的结果，不直接上传内部目录、登录信息、进程标识或终端截图。

最新服务器状态回执：2026-10-18 07:47:17，北京时间。对应 GPU 保存采样：同日 06:13:14。新回执到来后应追加实验记录并更新状态快照；不把历史快照的字段自动滚动成当前事实。
