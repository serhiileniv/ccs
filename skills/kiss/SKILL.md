---
name: kiss
description: Edit a draft, PR body, commit message, doc, or comment down to what it needs to be.
argument-hint: "[path | paste the text | empty = this session's last output]"
disable-model-invocation: true
---

# /ccs:kiss

Cut anything that doesn't change what the reader does next.

## Target

| Input | Acts on |
|---|---|
| a path | that file |
| pasted text | the text |
| empty | the most recent draft, PR body, or commit message from this session |

## The rule

Answer first. Bullets and numbers, not stories. One line where one line does.

Cut on sight:

- a paragraph restating the line above it
- narration of what was tried before it worked
- an essay in a test's docblock
- preamble before the answer
- hedging that adds no information
- a summary of what the reader just read

## The two carve-outs

These earn their words because nobody can recover them by reading the code:

1. **Why** a non-obvious decision was made
2. **What a check cannot do** — the limits of a test, a scan, a guarantee

Never cut these to save space. If the draft is missing them, that is a finding.

## Short ≠ dishonest

Caveats, risks, unverified claims, and disagreement still get said — in one line each.
Cutting a risk because it was inconvenient is a lie of omission.

## Preserve voice

Sharper, not blander. Distinctive phrasing stays. Do not neutralise a strong claim into a
balanced one.

## Output

Return the edited text. Then, in at most three bullets, name what was cut and why.

If the draft is already tight, say so and change nothing.
