# 🛤 road-not-taken

A Claude Code plugin that surfaces the **alternatives Claude silently rejected**.

Claude picks one approach and commits to it — that's what makes it useful.
But the rejected option is sometimes the right one *for your environment*:
you already run Redis, your team standardized on that library, your traffic
is 100x what Claude pictured. You just never find out the fork existed.

With `road-not-taken`, Claude appends a short footer after responses that
involved a real design choice, listing what it considered, why it lost, and
— most importantly — **the condition under which it would have won**.

## Example

> I've implemented rate limiting with an in-memory token bucket in
> `middleware/ratelimit.py`. Each user gets 100 requests/minute. ...
>
> ---
> 🛤 Roads not taken
> ⚖ Redis-backed limiter (needs infra; the right call if you run >1 instance)
> · nginx `limit_req` (zero app code, but you lose per-user custom limits)

Now instead of shipping a limiter that breaks the moment you scale to two
instances, you reply *"we deploy 4 replicas"* — and Claude switches to the
road it already mapped.

## Install

From a marketplace that includes this repo:

```
/plugin marketplace add TaeyangYeon/road-not-taken
/plugin install road-not-taken@road-not-taken
```

Or drop the skill in directly without the plugin system:

```bash
mkdir -p ~/.claude/skills/road-not-taken
curl -o ~/.claude/skills/road-not-taken/SKILL.md \
  https://raw.githubusercontent.com/TaeyangYeon/road-not-taken/main/skills/road-not-taken/SKILL.md
```

## What it does

After responses involving a genuine fork — library choice, architecture,
algorithm, debugging hypothesis, trade-off call — Claude appends:

```
---
🛤 Roads not taken
· <alternative> (<why it lost + when it would win>)
```

- **1–3 bullets max**, ordered by how likely you are to actually want the
  alternative — close calls get a `⚖`
- **Only defensible, divergent alternatives** — style choices and strawmen
  never appear
- **The winning condition is mandatory** — every bullet tells you exactly
  what fact would flip the decision
- **No padding** — if the task had one obvious approach, no footer appears

## The X-ray series

This is the third skill in a set that makes Claude Code's answers inspectable:

| Skill | Question it answers |
|-------|--------------------|
| [token-breakdown](https://github.com/TaeyangYeon/token-breakdown) | Where did the tokens go? |
| [assumption-log](https://github.com/TaeyangYeon/assumption-log) | What was assumed but not verified? |
| **road-not-taken** | What was considered but not chosen? |

Cost, premises, choices. All three are single-file skills — no hooks, no
scripts, no dependencies — and they compose: with assumption-log installed,
the two footers share one divider (Assumptions first, Roads not taken after).
Read the [SKILL.md](skills/road-not-taken/SKILL.md); that's the entire
implementation.

## Structure

```
road-not-taken/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── road-not-taken/
│       └── SKILL.md
└── README.md
```
