# grill-me

一个给「能弹卡片提问」的 agent 用的追问 skill：一次只问一个决策，直到方案里没有默认假设。

多数「拷问我」的提示词有两种典型失败：把四个问题塞进一条消息，或者用正文提问、等你打字回复。看起来像访谈，实际是一堵墙。这个 skill 用一份很小的约定把它们排除掉。

## 约定

- **一轮一问。** 一次提问调用，只带一个问题。
- **走提问工具。** 卡片才是让回答结构化的东西；正文提问不是退路，是本 skill 要消灭的失败形态。
- **推荐在前。** 每个问题都带排序选项，推荐项第一，附一句理由。
- **事实归 agent。** 能读的一律先查，不问你。
- **问完就停。** 不自问自答，不按假设往下规划。

访谈沿一棵**决策树**推进：每个答案敲定一个决策，解锁依赖它的那些问题；当前能问的集合就是前沿，逐个弹出，而不是成批抛出。

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

skill 写死了 DSH 的 `ask_user_question` 工具，并且没有纯文本退路：没有结构化提问工具的宿主上，访谈跑不起来，这个 skill 就不适用。Claude Code 的等价物是 `AskUserQuestion`，改个名字就是全部移植工作。

`disable-model-invocation: true` 让它不进入模型目录：由你手动调用，模型不会自己触发。

## 许可

MIT，见 [LICENSE](LICENSE)。

访谈方法（决策树、前沿、一次一问）改编自 [mattpocock/skills](https://github.com/mattpocock/skills) 的 `grilling`（MIT，Copyright (c) 2026 Matt Pocock），详见 [NOTICE](NOTICE)。
