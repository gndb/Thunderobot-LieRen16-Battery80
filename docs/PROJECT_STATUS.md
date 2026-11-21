# 猎刃 16 Battery80 项目状态 · Project status

更新于 2026-11-21。面板可在已经验证的猎刃 16（R16 / NLYA / TP181 / IT5570 rev07 / C009A0）上调用原厂固定 80% 维护策略。11 月的 Type-C 研究是另一个供电问题。继续使用现有的 [0.2.1 Windows 便携版](https://github.com/gndb/Thunderobot-LieRen16-Battery80/releases/tag/v0.2.1)时，请先看[使用与恢复](USAGE.md)。

## 80% 维护的事实

| 场景 | 原机结果 |
|---|---|
| 开启、回读、取消 | 已实测。启用时读回目标 80%，取消后回读默认状态并清除 Learn 主动放电请求。 |
| 关窗后重新打开 | 曾在重新打开后读到目标 80%。正常退出本身不会主动取消维护。 |
| 拔插后重新接电 | 2026-10-13，设置进程退出后仅拔插电源。临时只读观察看到目标由 D0 变成默认 3C，电量约 93% 时恢复充电 39.015 W。诊断没有发出取消命令。 |
| 接近 80% 的停充观察 | 有约一分钟的连续短样本，以及相隔约 22.5 分钟的两个 0 W 样本。仍缺从低电量充到目标、连续 30 分钟及两次完整充电过程的验收。 |
| 睡眠、重启与掉电 | 持久性仍待独立验证。 |

候选 EC 程序中，适配器断开事件存在可清除维护目标的分支。该分支与观察相符，但当次实际执行路径没有唯一确定，也还没有可验证的自动保存或恢复接口。重新接电、唤醒或重启后，请打开面板读回状态。维护已关闭时可手动开启，面板退出后没有自动重设。高于目标时开启可能触发原厂主动放电。[实测型号](TESTED_MODEL.md) · [验收范围](VALIDATION.md)

## Type-C 供电研究

保存的 TP181 固件表明，USB Charger 菜单可进入 EC 的输出控制逻辑，代码会结合平台状态和电池电量决定输出。具体对应的物理接口和保护开关仍需板级确认。

固件还包含一条原厂 PD 更新链，涉及 UEFI 模块、主机与 EC 之间的邮箱和 SMBus。研究区分了通信桥清理、设备复位请求和整机复位，也识别出原厂适配器状态里的 W140 是分类位。它不能提供实际 VBUS、协商功率或完整的 PD 合同。

后置 Type-C 的 USB 数据枚举与实际输入供电分别观察过。已有数据不足以确定供电故障原因、实物器件身份或可用的独立复位接口。静态研究没有新增实机修复验收，80% 面板仍只负责主电池维护。

## English summary

On the reference R16, the OEM 80% policy was enabled, read back and cancelled. After an ordinary close and reopen, the target still read 80%. In a separate AC unplug/replug observation on October 13, the policy state returned to default and charging resumed. Check the panel after reconnecting, waking, or rebooting and manually enable 80% if needed. The policy may actively discharge the battery above its target. Full charging cycles and retention across sleep or reboot remain unverified.

Static analysis linked the BIOS USB Charger setting to EC output policy and mapped the OEM USB-C PD update path at a high level. The installed PD controller, the failure cause, and a standalone reset procedure remain unconfirmed. This update documents the findings. The existing 0.2.1 binary is used for distribution. [Architecture](ARCHITECTURE.md) · [Validation](VALIDATION.md)
