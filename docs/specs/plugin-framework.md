# Spec: CCS — a distributable Claude Code plugin

## Goal

One command installs the whole setup on any machine:

```
/plugin marketplace add serhiileniv/ccs
/plugin install ccs@ccs
```

Three things make it distinctive, and nothing in ECC covers them:
**spec-first work**, **KISS output**, and **break-it-down** learning.

## Non-goals

- **Not an ECC competitor.** No skill catalog, no agent roster. ECC stays installed and
  handles the generic engineering loop; this plugin calls it.
- **No hooks in v1.** ECC ships 7. A second runtime means duplicate formatters and
  contradictory gates.
- **No git-identity tooling.** That is machine config, not a framework.
- **Nothing from `~/.claude/skills/`** (brand, grilling, no-ai-slop, …). Out of scope.

## Current state

| | |
|---|---|
| Repo | `github.com/serhiileniv/skills` → renaming to `ccs`, MIT |
| Contents | `skills/break-it-down/`, `skills/design/design.md` |
| Install | manual `cp -R` into `~/.claude/skills/` |

Spec-driven flow and KISS rules live only in `~/.claude/CLAUDE.md` — they apply to one
machine, are invisible to anyone else, and are advice rather than a workflow.

## What to adopt from ECC, and why

ECC's value is not its 386 skills — it is four mechanics worth copying deliberately:

| Mechanic | Why it works | How it lands here |
|---|---|---|
| **Two human gates** | Gate after plan, gate before commit. Everything between runs unattended. | `/ccs:spec` gates after the spec; v2 adds the commit gate |
| **Size classifier** | Ceremony scales to blast radius — a typo does not get a research phase | `/ccs:spec` refuses trivial work instead of ceremonially speccing it |
| **Compose, never reimplement** | `orch-*` are thin wrappers over `/plan`, `/code-review`, `tdd-workflow` | every command here delegates to ECC or to an existing skill |
| **Artifact handoff** | `/ecc:plan` writes `.plan.md`; the next phase reads it instead of re-deriving | `docs/specs/<slug>.md` is that artifact, and it already survives shipping |

Deliberately **not** adopted: instincts (a second learning store competing with
`memory/`), 94 legacy command shims, rule packs (plugins cannot distribute rules).

## Scope

**v1: `spec`, `learn`, `kiss`, `investigate`, `fix`.** A full gated pipeline command is
deferred — see Open questions 2.

## Proposed design

### Namespace

Plugin id `ccs@ccs` → commands resolve as `/ccs:<name>`, no collision with `/ecc:*`.
`break-it-down` keeps its bare name (renamed from `own-this` before the first tag).

### Layout

```
.claude-plugin/
  plugin.json        marketplace.json
commands/
  spec.md  learn.md  kiss.md  investigate.md  fix.md
skills/
  break-it-down/  design/
docs/specs/
```

### Commands

| Command | Does | Gate |
|---|---|---|
| `/ccs:spec <slug>` | Writes `docs/specs/<slug>.md`: goal, non-goals, current state, design, acceptance, open questions. Concrete design — formats, APIs, invariants — not prose. | stops for approval |
| `/ccs:learn` | Session → one fact per file in `~/.claude/projects/<slug>/memory/`, with `name`/`description`/`metadata.type` frontmatter and `[[wikilinks]]`; appends the `MEMORY.md` index line. | — |
| `/ccs:investigate <q>` | Read-only. How something works, or why it broke. Reproduce before theorising, cite `file:line`, mark inference as inference, name what could not be verified. Changes nothing, ever. | — |
| `/ccs:fix <what>` | Failing test that reproduces the bug **first**, watch it fail, smallest fix, full suite green, KISS commit body carrying the root cause. Design changes are refused — those are a spec. | — |
| `/ccs:kiss [path]` | Edits a draft, PR body, commit message, or doc to KISS: answer first, no narration of what was tried, no paragraph restating the line above. Two carve-outs survive — *why* a non-obvious decision was made, and *what a check cannot do*. | — |

KISS is also baked into `/ccs:spec` output, not only available on demand.

### Invariants

1. **Spec before code** for anything touching more than one subsystem. Trivial fixes skip it.
2. **The spec is updated in the same commit** when the design changes mid-implementation.
3. **Commands compose.** Anything ECC does well is called, never reimplemented.
4. **KISS applies to everything written** — docs, PR bodies, commit bodies, comments.

### Distribution

`marketplace.json` at repo root → `claude plugin marketplace add serhiileniv/ccs`.
Version tags drive updates. No npm package; nothing here needs a runtime.

## Acceptance criteria

- [ ] `claude plugin marketplace add serhiileniv/ccs` succeeds on a clean machine
- [ ] `claude plugin install ccs@ccs` registers all five commands as `/ccs:*`
- [ ] `break-it-down` resolves under its new name; no `own-this` references remain
- [ ] `/ccs:spec` output matches the section shape above and stops for approval
- [ ] `/ccs:learn` writes a memory file that loads on the next session
- [ ] ECC stays installed and functional; no duplicated hooks
- [ ] `claude plugin details ccs@ccs` reports always-on cost under 3k tokens

## Open questions

1. ~~**Namespace.**~~ Settled: `ccs@ccs`, expanding to Cut To Essence. Locked once tagged.
2. **Does a `/ccs:feature` pipeline earn its place** in v2, or is it
   `/ecc:orch-add-feature` with a spec bolted on? Deferred until `spec`, `learn` and
   `kiss` have been used on real work.
3. **Does `/ccs:fix` duplicate `/ecc:orch-fix-defect`?** It was built anyway, deliberately:
   the lean path when the cause is already known, with the `/ccs:investigate` handoff and a
   KISS commit body. Revisit if both end up used interchangeably.
4. **Is `kiss` a skill, a command, or both?** A skill triggers on its own when writing;
   a command is explicit. Shipping both may be redundant.
5. **Does `design.md` become a skill** or stay a plain guide pointed at by hand?
