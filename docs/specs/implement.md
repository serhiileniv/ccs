# Spec: `/ccs:implement` — one test-first path for all code changes

## Goal

One skill writes all code, bugs and features alike, using TDD: red → green → refactor,
one test at a time. It replaces `/ccs:fix`. Upstream skills hand it their output:
a spec's acceptance criteria, or a root cause from `root-cause`.

## Non-goals

- **Test-framework setup.** A repo without tests needs a dependency choice; that is a spec.
- **Coverage targets, mutation testing.** They measure the tests, not the behaviour.
- **Hooks enforcing test-first.** The plugin has no hooks by design (`plugin-framework.md`).
- **Keeping `/ccs:fix` as an alias.** Two commands for one path is the duplication invariant 3 forbids.

## Current state

| | |
|---|---|
| `skills/fix/SKILL.md` | Test-first, bugs only: failing test, watch it fail, smallest fix, suite green, commits with root cause. |
| `skills/spec/SKILL.md` "After approval" | "implement against it" (no method given). |
| `skills/investigate/SKILL.md:14,51` | Hands off to `/ccs:fix`. |
| `README.md:13` | Lists `/ccs:fix`. |
| `docs/specs/plugin-framework.md` | `fix` row; Open questions 2 (`/ccs:feature` deferred) and 3 (`fix` vs `ecc:orch-fix-defect`). |
| Feature TDD | None in `skills/`, README or specs. |

## Proposed design

### Skill

`skills/implement/SKILL.md`, model-invocable. `skills/fix/` deleted.

```yaml
name: implement
description: Write code test-first (red, green, refactor, one test at a time), for bugs and features alike. Use to implement an approved spec, fix a bug, or add behaviour.
argument-hint: "<bug | behaviour | docs/specs/<slug>.md>"
```

### Steps

| # | Step | Rule |
|---|---|---|
| 0 | Route | Non-trivial with no spec → `/ccs:spec`. Bug with unknown cause → `/ccs:root-cause`, then take its root cause. No test runner in repo → stop, say so. Design change mid-bug → stop, `/ccs:spec`. |
| 1 | Test list | Spec → one line per acceptance criterion. Bug → one line: the reproduction. Otherwise from the request. A criterion no test can check → `manual: <why>`, not dropped. Show the list before coding. |
| 2 | Red | One test, in the repo's existing test style and location. It asserts the behaviour, not the current output. Run it. **Watch it fail for the stated reason**: a compile error or setup failure does not count as red. |
| 3 | Green | Smallest code that passes it. No code for tests not yet written. No refactors, cleanups or renames mixed into this step. |
| 4 | Refactor | Only on green. Run the full suite after. |
| 5 | Next | Tick the item, back to 2. New behaviour or a second bug found on the way → append to the list; don't build it silently. |
| 6 | Done | Full suite, typecheck, lint pass: run them, don't assume. Report each list item → test name or `manual`, plus actual suite output. Design changed → update the spec in the same change (spec rule). |

Bug reports add the root cause in one line. No commits: the user commits.

Ends with the shared `## Writing` block, byte-identical to `skills/spec/SKILL.md`.

### Wiring

| File | Change |
|---|---|
| `skills/fix/` | Deleted |
| `skills/spec/SKILL.md` "After approval" | → `implement it with /ccs:implement docs/specs/<slug>.md` |
| `skills/investigate/SKILL.md:14,51` | `/ccs:fix` → `/ccs:implement` |
| `README.md` | `fix` row → `/ccs:implement <what>`: "Test-first for bugs and features: failing test → smallest code → refactor → suite green." |
| `plugin.json` | `0.2.0` → `0.3.0` (removing a command is breaking) |
| `docs/specs/plugin-framework.md` | Header note: `fix` superseded by [implement](implement.md). Open questions 2 and 3 marked settled by it. |

## Acceptance criteria

- [ ] `skills/implement/SKILL.md` exists with the frontmatter above; `skills/fix/` is gone
- [ ] `grep -rn "ccs:fix" skills README.md .claude-plugin` is empty
- [ ] Its `## Writing` block matches `skills/spec/SKILL.md` exactly
- [ ] Given a spec path, the step 1 list has one line per acceptance criterion, none dropped
- [ ] Given a bug with a known cause, the list is one reproduction test, and it runs red before any fix
- [ ] Transcript shows every test's failing run before the code that makes it pass
- [ ] Final report includes actual suite output; no commit is made
- [ ] After reinstall, `/ccs:implement` is listed and `/ccs:fix` is not

Criteria 4–7 describe behaviour. They are checked by running the skill once on a real spec and once on a real bug, not by grep.

## Open questions

1. ~~**Commit?**~~ Settled: no. Report and stop; the user commits.
