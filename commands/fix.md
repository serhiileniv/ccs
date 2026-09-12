---
description: Fix a bug — failing test that reproduces it first, then the fix, then green.
argument-hint: "<what is broken | the output of /sl:investigate>"
---

# /sl:fix

A bug is not fixed until a test that used to fail now passes.

## Step 1 — know the cause

If the root cause is not established, stop and run `/sl:investigate` first. Fixing a
symptom you have not traced produces a second bug behind the first.

Accept the handoff: if `/sl:investigate` already ran, take its root cause and evidence
rather than re-deriving them.

## Step 2 — reproduce it as a failing test

Write the test **before** touching the implementation. It must:

- fail for the reason the bug exists, not for a setup mistake
- assert the behaviour, not the current broken output
- live where the repo already keeps its tests, in the style already used there

Run it. **Watch it fail.** A test that passes before the fix proves nothing, and a test
you never saw fail is not evidence.

## Step 3 — fix

Smallest change that makes the test pass. No refactoring alongside it, no drive-by
cleanups, no renaming — those are `/ecc:refactor-clean`, in their own commit.

If the fix requires a design change, stop: that is a spec, not a bug. Run `/sl:spec`.

## Step 4 — green

- the new test passes
- **the rest of the suite still passes** — run it, do not assume
- if the repo has a typecheck or lint gate, it passes too

Report actual output. If something fails, say so with the output; never report a fix as
done on an unrun suite.

## Step 5 — commit

Conventional subject, KISS body:

```
fix: <what now works, not what you did>

<One line on the root cause — why it broke, which is the thing nobody can
recover by reading the diff.>
```

No narration of the debugging. No list of what was tried. The failing test already
documents the symptom; the body documents the *cause*.

End with the attribution trailer the environment specifies.

## Scope

One bug, one fix, one commit. A second bug found on the way gets written down and fixed
separately — not folded in.

> Want the whole gated pipeline instead (research → plan → TDD → review → commit),
> use `/ecc:orch-fix-defect`. This command is the lean path: cause is known, fix it.
