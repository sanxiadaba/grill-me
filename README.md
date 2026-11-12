# grill-me

A grilling skill for agents that can ask structured questions: it interviews you one
decision at a time, through a real question card, until the plan has no silent
assumptions left.

Most "grill me" prompts fail in two predictable ways: the agent dumps four questions
into one message, or it asks in prose and waits for you to type a reply. Both look like
an interview and behave like a wall of text. This skill is a small contract that rules
them out.

## Behavior contract

- **One question per turn.** Exactly one question tool call, exactly one item in it.
- **Through the question tool.** The card is what makes the answer structured; prose
  questions are not a fallback, they are the failure mode this skill removes.
- **Recommendation first.** Every question carries your options ranked, the recommended
  one first, with a one-line reason.
- **Facts are the agent's job.** If it can be read from disk, the repo or the web, the
  agent looks it up instead of asking you.
- **Stop and wait.** No self-answering, no planning ahead on an assumed answer.

The interview runs over a **design tree**: each answer settles a decision and unlocks the
questions that depended on it. The set of currently askable decisions is the *frontier*,
and the agent pops it one item at a time instead of batching.

## Install

Copy the directory into a scanned skill root:

```powershell
git clone https://github.com/sanxiadaba/grill-me.git "$env:USERPROFILE\.agents\skills\grill-me"
```

DSH scans `~/.agents/skills` (rank 500) and watches it, so the skill appears without a
restart. For Claude Code, place it in `~/.claude/skills/grill-me` instead.

## Usage

```
/grill-me
```

Then state the plan, feature, or decision you want stress-tested. Expect one card per
round, not a questionnaire.

## Compatibility

The skill is written against the `ask_user_question` tool, which is the DSH name for a
structured question card, and it has no plain-text fallback: on a host without such a
tool the interview cannot run, so the skill does not apply. Claude Code's equivalent is
`AskUserQuestion`; substituting that name is the whole port.

`disable-model-invocation: true` keeps the skill out of the model's automatic catalog:
you invoke it, it does not volunteer.

## License

MIT. See [LICENSE](LICENSE).

The interview method (design tree, frontier, one question at a time) is derived from the
`grilling` skill in [mattpocock/skills](https://github.com/mattpocock/skills) (MIT,
Copyright (c) 2026 Matt Pocock). See [NOTICE](NOTICE).
