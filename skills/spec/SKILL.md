---
name: spec
description: Write a design spec at docs/specs/<slug>.md, then stop for approval. Refuses trivial work. Use before a feature, redesign, or change touching more than one subsystem.
argument-hint: "<slug or short description>"
---

# /ccs:spec

Non-trivial work starts with a spec, not code. This writes it, shows it, and stops.

## Step 0 — refuse trivial work

A typo, a one-line bug, an obvious test does **not** get a spec. Say so in one line and
stop. Ceremony scales to blast radius.

Write a spec when the change touches more than one subsystem, adds a dependency or a
public contract, or has a real design choice in it.

## Step 1 — read before writing

Ground the spec in what exists. Never describe the current state from memory:

- the files the change would touch, and the conventions already in them
- prior specs in `docs/specs/`
- what the repo already does that this would duplicate

If nothing similar exists, say that explicitly. Do not invent a pattern to mirror.

## Step 2 — write `docs/specs/<slug>.md`

Create the directory if missing. Slug: lowercase, hyphens, no dates.

```markdown
# Spec: <title>

## Goal
What this is for, in two or three sentences. Not a feature list.

## Non-goals
What this deliberately does not do, each with the reason it was excluded.

## Current state
What exists today, with file paths. Facts read from the repo, not recollection.

## Proposed design
Concrete. Layouts, formats, APIs, invariants, command tables, file trees.
A reader must be able to build from this without asking what you meant.

## Acceptance criteria
- [ ] Checkable statements. Each one either passes or fails.

## Open questions
Numbered. Only what the spec genuinely cannot settle — not questions you could
answer by reading the code.
```

## Step 3 — KISS the whole thing

Before showing it, apply the `/ccs:kiss` rules to the draft.

## Step 4 — gate

Report the path. State the open questions as questions. Then **stop**.

Do not write code until the user approves. "modify: …" means revise the spec and gate
again.

## After approval

The spec is the design record, not scaffolding:

- implement against it
- when the design changes mid-implementation, **update the spec in the same commit**
- keep it after shipping — `docs/` stays the user-facing doc, `docs/specs/` is the record
