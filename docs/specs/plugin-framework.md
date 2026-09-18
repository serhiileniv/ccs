# Spec: CCS — a distributable Claude Code plugin

> `kiss`, `learn` and `break-it-down` are superseded by [kiss-and-learn](kiss-and-learn.md).
> `fix` is superseded by [implement](implement.md); `investigate` by [root-cause](root-cause.md).

## Goal

One command installs the whole setup on any machine:

```
/plugin marketplace add serhiileniv/ccs
/plugin install ccs@ccs
```

Three things it is for: **spec-first work**, **KISS output**, and learning that
outlives the session.

## Non-goals

- **Not an ECC competitor.** No skill catalog, no agent roster. Works with or without ECC;
  no skill depends on it.
- **No hooks in v1.** If ECC is installed it ships 7; a second runtime means duplicate
  formatters and contradictory gates.
- **No git-identity tooling.** That is machine config, not a framework.
- **Nothing from `~/.claude/skills/`** (brand, grilling, no-ai-slop, …). Out of scope.

## Current state

| | |
|---|---|
| Repo | `github.com/serhiileniv/ccs`, MIT |
| Contents | `skills/{spec,learn,kiss,investigate,fix,break-it-down}/`, `docs/guides/design.md` |
| Install | `/plugin install ccs@ccs`, or copy a single skill by hand |

Spec-driven flow and KISS rules live only in `~/.claude/CLAUDE.md` — they apply to one
machine, are invisible to anyone else, and are advice rather than a workflow.

## Scope

**v1: `spec`, `learn`, `kiss`, `investigate`, `fix`.** A full gated pipeline command is
deferred — see Open questions 2.

## Proposed design

### Namespace

Plugin id `ccs@ccs` → skills resolve as `/ccs:<name>`, no collision with `/ecc:*`.

### Layout

```
.claude-plugin/
  plugin.json        marketplace.json
skills/
  spec/  learn/  kiss/  investigate/  fix/  break-it-down/    each with SKILL.md
docs/
  specs/  guides/design.md
```

### Skills

`commands/` is the legacy layout; everything is a skill. `kiss`, `learn` and `break-it-down`
set `disable-model-invocation: true` — user-invoked, zero always-on context.

| Command | Does | Gate |
|---|---|---|
| `/ccs:spec <slug>` | Writes `docs/specs/<slug>.md`: goal, non-goals, current state, design, acceptance, open questions. Concrete design — formats, APIs, invariants — not prose. | stops for approval |
| `/ccs:learn` | Explicit trigger for Claude Code's built-in memory rules. Does not restate them — a copy drifts from the harness. | — |
| `/ccs:investigate <q>` | Read-only. How something works, or why it broke. Reproduce before theorising, cite `file:line`, mark inference as inference, name what could not be verified. Changes nothing, ever. | — |
| `/ccs:fix <what>` | Failing test that reproduces the bug **first**, watch it fail, smallest fix, full suite green, KISS commit body carrying the root cause. Design changes are refused — those are a spec. | — |
| `/ccs:kiss [path]` | Edits a draft, PR body, commit message, or doc to KISS: answer first, no narration of what was tried, no paragraph restating the line above. Two carve-outs survive — *why* a non-obvious decision was made, and *what a check cannot do*. | — |

KISS is also baked into `/ccs:spec` output, not only available on demand.

### Invariants

1. **Spec before code** for anything touching more than one subsystem. Trivial fixes skip it.
2. **The spec is updated in the same commit** when the design changes mid-implementation.
3. **Commands compose.** A command that duplicates one that already exists is a bug;
   call the existing one.
4. **Ceremony scales to blast radius.** A typo does not get a spec, and a cross-cutting
   change does not skip one.
5. **Each command hands the next one an artifact**, so nothing is re-derived from scratch:
   `docs/specs/<slug>.md` from `/ccs:spec`, a root cause from `/ccs:investigate`.
6. **KISS applies to everything written** — docs, PR bodies, commit bodies, comments.

### Distribution

`marketplace.json` at repo root → `claude plugin marketplace add serhiileniv/ccs`.
Version tags drive updates. No npm package; nothing here needs a runtime.

## Acceptance criteria

- [ ] `claude plugin marketplace add serhiileniv/ccs` succeeds on a clean machine
- [ ] `claude plugin install ccs@ccs` registers all six skills as `/ccs:*`
- [ ] No skill references a command outside this plugin
- [ ] `/ccs:spec` output matches the section shape above and stops for approval
- [ ] `/ccs:learn` writes a memory file that loads on the next session
- [ ] If ECC is installed, it stays functional; no duplicated hooks
- [ ] `claude plugin details ccs@ccs` reports always-on cost under 3k tokens

## Open questions

1. ~~**Namespace.**~~ Settled: `ccs@ccs`. Locked once tagged.
2. ~~**Does a `/ccs:feature` pipeline earn its place** in v2, or is it
   `/ecc:orch-add-feature` with a spec bolted on? Deferred until `spec`, `learn` and
   `kiss` have been used on real work.~~ Settled by [implement](implement.md): `spec → implement`.
3. ~~**Does `/ccs:fix` duplicate `/ecc:orch-fix-defect`?** Built anyway, deliberately: the
   lean path when the cause is known. Revisit if both end up used interchangeably.~~ Superseded by [implement](implement.md).
4. ~~**Is `kiss` a skill, a command, or both?**~~ Superseded by [kiss-and-learn](kiss-and-learn.md). Was: a user-invoked skill. The
   always-on version lives in `~/.claude/CLAUDE.md`.
5. ~~**Does `design.md` become a skill?**~~ Settled: a plain guide in `docs/guides/`.
