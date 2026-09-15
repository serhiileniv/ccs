---
name: learn
description: Distil the session into durable memory files, following Claude Code's memory rules.
argument-hint: "[what to remember]"
disable-model-invocation: true
---

# /ccs:learn

Review this session and save what qualifies as memory, following the memory instructions
in your system prompt: one fact per file, check for duplicates first, index in `MEMORY.md`.
If `$ARGUMENTS` is given, save that.

Write each `description` as the fact itself, not its topic — it decides recall.

Report what was written, one line each. If nothing qualified, say so; an empty run is
better than filler.
