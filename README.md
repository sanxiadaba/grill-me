# grill-me

给「能弹卡片提问」的 agent 用的追问 skill：一次一个决策，沿决策树问到底，直到方案里没有默认假设。

方法来自 Matt Pocock 的 [grilling](https://github.com/mattpocock/skills)，主干保留——设计树、前沿、事实归 agent、决策归用户、确认前不动手。只改了一处：原版一次问完整个前沿，这里改成一次一张卡、只问一个问题。在对话界面里成批抛问题，会把「思考」降级成「填表」。

## 怎么工作

- **设计树**：每个决策分叉出挂在它下面的决策。
- **前沿**：前置已定、现在就能问的那批决策。每答一次，前沿往外推一截。
- **一轮一问**：一张卡只放一个问题，附推荐答案；问题写成「是」就等于接受推荐。
- **事实归 agent**：需要环境事实就派子 agent 去查，不问你。
- **决策归你**：逐个交给你，然后等。
- **确认前不动手**：前沿空了才复述共识、请求确认，确认之后才执行。

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
