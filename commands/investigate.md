---
description: Read-only. Explain how something works, or why it broke, with evidence. Changes nothing.
argument-hint: "<question or symptom>"
---

# /sl:investigate

Two questions, one discipline: **how does this work**, and **why did it break**.

## The hard rule

**Nothing is modified. Ever.** No edits, no fixes, no "while I was in there". If the
answer implies a change, name the change and stop — the fix is `/sl:fix`.

Read-only commands are fine (`git log`, `git diff`, `git blame`, tests that do not write,
log reads). Anything that mutates state is out of scope even when it looks harmless.

## Method

### 1. State the question back

One line. If the symptom is vague ("it's broken"), pin it down first: what was expected,
what happened, what changed since it last worked.

### 2. Reproduce before theorising

For a failure: get it to happen, or say plainly that you could not. An unreproduced bug
with a confident cause is a guess wearing a lab coat.

### 3. Follow evidence, not plausibility

Trace the actual path — imports, call sites, data flow, config, the commit that changed
it. Cite `file:line` for every claim. If you infer something you did not read, mark it as
inference.

### 4. Narrow

Bisect. Cut the space in half, not one candidate at a time. `git log -S`, `git bisect`,
disabling half the input, the last green commit.

## Output

```
Question: <restated in one line>

Answer: <the mechanism, or the root cause — first, before any detail>

Evidence:
  - file.ts:42 — <what this line proves>
  - <command output, if it decided something>

Not verified: <what you could not confirm, and why>

If you want it fixed: /sl:fix <one-line description of the change>
```

Root cause means the thing that, changed, makes the symptom impossible — not the line
that threw. If you only reached a proximate cause, say so.

## KISS

The answer goes first. No retelling of the search. No list of the theories you discarded
unless one of them is *still* live and the user needs to choose.

Two things earn words: **why** the system is built the non-obvious way it is, and **what
you could not check**.
