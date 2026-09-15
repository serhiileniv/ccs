---
name: fix
description: Fix a bug — failing test that reproduces it first, then the fix, then green. Use when the cause of a bug is known (or /ccs:investigate found it) and it needs fixing.
argument-hint: "<what is broken | the output of /ccs:investigate>"
---

# /ccs:fix

A bug is not fixed until a test that used to fail now passes.

## Step 1 — know the cause

If the root cause is not established, run `/ccs:investigate` first. Fixing a symptom you
have not traced produces a second bug behind the first.

If `/ccs:investigate` already ran, take its root cause and evidence rather than
re-deriving them.

## Step 2 — reproduce it as a failing test

Write the test **before** touching the implementation. It must:

- fail for the reason the bug exists, not for a setup mistake
- assert the behaviour, not the current broken output
- live where the repo already keeps its tests, in the style already used there

Run it. **Watch it fail.** A test you never saw fail is not evidence.

## Step 3 — fix

Smallest change that makes the test pass. No refactoring, cleanups, or renames alongside
it — those go in their own commit.

If the fix requires a design change, stop: that is a spec, not a bug. Run `/ccs:spec`.

## Step 4 — green

- the new test passes
- **the rest of the suite still passes** — run it, do not assume
- the repo's typecheck and lint gates pass, if it has them

Report actual output. Never report a fix as done on an unrun suite.

## Step 5 — commit

```
fix: <what now works, not what you did>

<One line on the root cause — the thing nobody can recover by reading the diff.>
```

No debugging narration. The test documents the symptom; the body documents the *cause*.
End with the attribution trailer the environment specifies.

## Scope

One bug, one fix, one commit. A second bug found on the way is written down and fixed
separately.
