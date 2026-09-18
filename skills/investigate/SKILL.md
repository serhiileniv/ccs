---
name: investigate
description: Read-only. How something works, or why it broke, with file:line evidence. Changes nothing. Use when asked how code works, why something fails, or before fixing a bug whose cause is unknown.
argument-hint: "<question or symptom>"
---

# /ccs:investigate

Two questions, one discipline: **how does this work**, and **why did it break**.

## The hard rule

**Nothing is modified. Ever.** No edits, no fixes, no "while I was in there". If the
answer implies a change, name it and stop — the fix is `/ccs:fix`.

Read-only commands are fine (`git log`, `git diff`, `git blame`, tests that do not write,
log reads). Anything that mutates state is out, even when it looks harmless.

## Method

1. **State the question back** in one line. If the symptom is vague, pin it down: what
   was expected, what happened, what changed since it last worked.
2. **Reproduce before theorising.** Get the failure to happen, or say plainly that you
   could not. An unreproduced bug with a confident cause is a guess.
3. **Follow evidence, not plausibility.** Trace imports, call sites, data flow, config,
   the commit that changed it. Cite `file:line` for every claim; mark inference as
   inference.
4. **Narrow by halves.** `git log -S`, `git bisect`, disabling half the input, the last
   green commit.

## Writing

One line where one line does. Answer first. State the fact, not the story behind it — no
narrating what was tried, no paragraph restating the line above.
Two things always stay: **why** a non-obvious decision was made, and **what a check cannot do**.
Caveats and risks still get said, one line each.

## Output

```
Question: <restated in one line>

Answer: <the mechanism, or the root cause — first, before any detail>

Evidence:
  - file.ts:42 — <what this line proves>
  - <command output, if it decided something>

Not verified: <what you could not confirm, and why>

If you want it fixed: /ccs:fix <one-line description of the change>
```

Root cause means the thing that, changed, makes the symptom impossible — not the line
that threw. If you only reached a proximate cause, say so.

No retelling of the search. Mention a discarded theory only if it is *still* live and the
user needs to choose.
