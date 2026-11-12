---
name: grill-me
description: "A relentless one-question-at-a-time interview to sharpen a plan or design. Use when the user wants to stress-test their thinking, or uses any 'grill' trigger phrase."
disable-model-invocation: true
---

# Hard rules

1. **One question per turn.** A turn means exactly one `ask_user_question` call containing exactly one entry in its `questions` array. Never write Q1/Q2/Q3 in one message. Never ask a second question before the first one is answered.
2. **Always ask through the `ask_user_question` tool.** That call is what renders the GUI question card. Do not ask in prose and wait for a chat reply. Prose is allowed only when the tool is genuinely unavailable (for example you are a sub-agent with no user-facing channel); then ask one question as plain text and stop.
3. **Lead with your own answer.** The question body states the decision, the trade-off, and your recommendation. The user confirms or corrects; they do not do your analysis.
4. **Use options when the answer is enumerable.** Two to four short labels, the recommended one first with a `(Recommended)` suffix, one-sentence `description` each. When it is genuinely open-ended, omit `options` and let the user type.
5. **Facts are yours, decisions are theirs.** Anything obtainable from the filesystem, the repo, the web, or a sub-agent must be looked up before asking. Never ask the user something you could read.
6. **Stop after the call.** No follow-up text, no planning as if the answer had arrived.

# Example call

```json
{"questions":[{"id":"storage","header":"Storage",
"question":"会话历史存哪里？我倾向 SQLite：单文件、可查询、迁移成本最低。",
"options":[{"label":"SQLite (Recommended)","description":"单文件、支持查询，迁移成本最低。"},
{"label":"JSON 文件","description":"零依赖，但并发与查询能力弱。"}]}]}
```

# The interview

Map the work as a **design tree**: every decision branches into the decisions that hang off it.

The **frontier** is the set of decisions whose prerequisites are already settled — decisions you can ask about now without guessing. Treat the frontier as a **queue, not a round**: pop exactly one item, preferring the question that unblocks the most downstream branches. Ask it, stop, wait for the answer.

Each answer settles a node and pushes the frontier outward; recompute and ask the next single question. A question whose prerequisite is still open is not asked yet.

Done when the frontier is empty: write one short paragraph stating the shared understanding, then ask for exactly one confirmation through the tool (options: `确认 (Recommended)` / `还有分歧`). Do not act on the plan before that confirmation.

# Anti-patterns — the failures this skill exists to prevent

- One message with Q1..Q4 -> one call, one question.
- Asking in prose and hoping for a reply -> a tool call that renders the card.
- Options without a recommendation -> recommended option first, marked.
- Asking the user for a fact you could grep -> look it up, then ask only the decision.
