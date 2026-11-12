# grill-me

给「能弹卡片提问」的 agent 用的追问 skill：一次一个决策，沿决策树问到底，直到方案里没有默认假设。

方法来自 Matt Pocock 的 [grilling](https://github.com/mattpocock/skills)，主干保留——设计树、前沿、事实归 agent、决策归用户、确认前不动手。改了三处：

- 原版一次问完整个**前沿**，这里改成一次一张卡、只问一个决策；
- 原版「回答『是』就等于接受推荐」，在卡片里没有「是」，改成推荐项排第一、选第一项即接受；
- 补上**阶段边界**：访谈期只读，而「用户要的就是调研本身」时，调研算交付物、不算查事实——缺这一条，agent 会在访谈做完之前就把活干了。

## 怎么工作

- **两阶段**：访谈期只读（提问 + 为提问查事实），执行期才动手；分界是你对共识的明确确认。
- **设计树**：每个决策分叉出挂在它下面的决策。
- **前沿**：前置已定、现在就能问的那批。一次只问一个；几个都解锁时，先问能解锁下游最多的那个。
- **提案，不是问卷**：每张卡都带推荐项和理由，推荐项排第一。
- **事实归 agent，但有门槛**：只在会改变下一张卡的问题时才查，查证不写文件、不问你。
- **调研即交付物**：你要的就是调研本身时，它不是提问的原料，确认之前不跑。
- **决策归你**：逐个交给你，然后等。
- **会收敛**：只影响措辞、命名、顺序，或答案会由实现自然确定的，记成假设不问；你说「够了」就收尾。
- **确认前不动手**：前沿空了才复述共识、请求确认，确认之后才执行。
- **确认是闸门**：沉默、改话题都不算确认；也不要在确认后回头再问已经谈定的东西。

## 安装

```powershell
git clone https://github.com/sanxiadaba/grill-me.git "$env:USERPROFILE\.agents\skills\grill-me"
```

DSH 扫描 `~/.agents/skills`（rank 500）并带 watcher，装好即生效、不用重启。Claude Code 放到 `~/.claude/skills/grill-me`。

## 用法

```
/grill-me
```

然后说你要被盘问的方案、功能或决策。预期是每轮一张卡，而不是一张问卷。

## 兼容性

skill 写死了 DSH 的 `ask_user_question`，没有纯文本退路：宿主没有结构化提问工具时，这个 skill 不适用。Claude Code 的等价物是 `AskUserQuestion`，改个名字就是全部移植工作。

`disable-model-invocation: true` 让它不进入模型目录：由你手动调用，模型不会自己触发。

## 许可

MIT，见 [LICENSE](LICENSE)。

访谈方法改编自 [mattpocock/skills](https://github.com/mattpocock/skills) 的 `grilling`（MIT，Copyright (c) 2026 Matt Pocock），详见 [NOTICE](NOTICE)。
