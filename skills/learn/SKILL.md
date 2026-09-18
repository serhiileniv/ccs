---
name: learn
description: Turn this session's lessons into edits to the ccs skills themselves, then commit on approval.
argument-hint: "[what to learn | empty = review this session]"
disable-model-invocation: true
---

# /ccs:learn

The skills get better with use. This turns what a session taught into edits to them.
If `$ARGUMENTS` is given, that is the lesson.

## Step 1 — find lessons

A lesson is one of:

- a user correction a ccs skill should have prevented
- a step that was skipped or misread because its wording allowed it
- a rule that proved wrong

Project facts and one-off preferences are not lessons — the harness memory holds those.
Never write memory files, and never edit `~/.claude/CLAUDE.md`.

## Step 2 — map each to one skill

Each lesson goes to one skill in `skills/`. No skill fits → report it, don't edit, don't
scaffold a new one.

## Step 3 — locate the source

`$CCS_REPO`, else `~/Projects/ccs`. It must be a git checkout whose `origin` is
`serhiileniv/ccs`. Otherwise print the proposed diff and stop.

Never edit `~/.claude/plugins/cache` — it is overwritten on update.

## Step 4 — edit

- Tree dirty → stop and say so.
- Branch `learn/<slug>` off `main`.
- Smallest change to that skill's `SKILL.md` or supporting file. Tighten an existing line
  before adding one.

## Step 5 — drift

Every skill carries the same `## Writing` block. If any differs from the one in
`skills/spec/SKILL.md`, make it match in the same diff.

## Step 6 — gate

Show the diff, then one line per lesson: *what happened → what changed*. Stop.

On approval, commit on the branch. Never push or open a PR.

Nothing qualified → say so and change nothing. An empty run beats filler.

## Writing

One line where one line does. Answer first. State the fact, not the story behind it — no
narrating what was tried, no paragraph restating the line above.
Two things always stay: **why** a non-obvious decision was made, and **what a check cannot do**.
Caveats and risks still get said, one line each.
