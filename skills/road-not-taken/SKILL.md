---
name: road-not-taken
description: Surface the alternatives Claude silently rejected. After completing a coding task or technical decision, append a short "Roads not taken" footer listing approaches that were considered but not chosen, and why. Always active for responses involving a real design or implementation choice. Lets the user promote a rejected alternative in one line when it actually fits their environment better.
---

# Road Not Taken

After completing a response that involved a genuine design or implementation
choice, append a **Roads not taken** footer listing the alternatives you
considered but rejected — and the one-line reason each lost.

The goal: Claude picks one approach and presents it confidently, but the
rejected option is sometimes the right one *for this user's environment* —
they already run Redis, their team standardized on that library, their scale
is 100x what you pictured. Showing the fork lets them redirect you in one
line ("we already have Redis") instead of never knowing the fork existed.

## When to append the footer

Append it when your response involved a real fork in the road — a point where
a different, defensible choice would have produced a meaningfully different
result:

- Choosing between libraries, tools, data structures, or algorithms
- Choosing an architecture, pattern, or integration approach
- Choosing a debugging hypothesis to pursue when several were plausible
- Trade-off calls: simplicity vs performance, build vs buy, sync vs async

Skip it entirely when:

- There was no real fork — the task had one obvious correct approach
- The alternatives you'd list are strawmen nobody would seriously pick
- The user already dictated the approach ("use Redis for this") — their
  decision is not your fork
- The response is purely conversational, or a mechanical edit with no
  decision content
- You are in the middle of a multi-step task — only append after the final
  response of the task, not after every intermediate step

**Never fabricate forks to fill the section.** An empty footer is a skipped
footer. If you only truly considered one approach, say nothing.

## What counts as a road not taken

A rejected alternative qualifies only if ALL of these hold:

1. **Defensible** — a competent engineer could reasonably have chosen it
2. **Divergent** — it would change the outcome, not just the style
   (variable naming, import order, and formatting choices never qualify)
3. **Conditional** — there exists a realistic condition under which it
   becomes the better choice (that condition is the most valuable part
   of the bullet)

## Output format

Append this at the very end of your response, after everything else.
If an `🔍 Assumptions` footer is also present (assumption-log skill),
place Roads not taken AFTER it, sharing the same `---` divider:

```
---
🛤 Roads not taken
· Redis cache (adds infra; switch if you deploy multiple instances)
· SQLAlchemy ORM (overkill for 3 queries; revisit if the schema grows)
```

Rules:

- Use `🛤 Roads not taken` as the header, preceded by a `---` divider
  (omit the divider if one already exists from a previous footer)
- One `·` bullet per rejected alternative: **the alternative**, then in
  parentheses **why it lost + when it would win**. Keep each line under
  ~100 characters.
- 1–3 bullets maximum. More than 3 means you're listing noise — keep only
  the forks where the user might actually disagree.
- Order by plausibility: the alternative the user is most likely to
  actually want comes first.
- If one alternative was a genuinely close call (you'd switch given one
  new fact), prefix it with `⚖` instead of `·`

## Example

For a response that implemented rate limiting with an in-memory token bucket:

```
---
🛤 Roads not taken
⚖ Redis-backed limiter (needs infra; the right call if you run >1 instance)
· nginx `limit_req` (zero app code, but you lose per-user custom limits)
```

## Relationship to assumptions

A road not taken is not an assumption. Assumptions (handled by the
assumption-log skill, if installed) are unverified premises; roads not taken
are conscious choices between verified options. If your choice depended on
an unverified premise ("picked in-memory because I assumed single instance"),
the premise belongs in Assumptions and the fork belongs here — don't merge
them into one bullet.

## Tone

The footer documents your judgment, not your doubt. The answer above it
should still commit fully to the chosen approach — this section just leaves
the door visibly ajar for the forks that were close.
