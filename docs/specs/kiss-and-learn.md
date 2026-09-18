# Spec: KISS built in, `learn` improves the skills

## Goal

KISS is how every ccs skill writes, not a separate command. `/ccs:learn` turns what a
session taught into edits to the ccs skills themselves, so the plugin gets better with use.
`break-it-down` is renamed `explain`.

## Non-goals

- **No memory files from `learn`.** Claude Code's built-in memory already covers project and
  user facts; a second path duplicates it.
- **No edits to `~/.claude/CLAUDE.md` from `learn`.** It's the user's file, not the plugin's.
- **No new skills from `learn`.** A lesson that needs one is reported, not scaffolded.
- **`fix` step 5 (commits unasked)** — separate change.

## Current state

| | |
|---|---|
| `skills/kiss/SKILL.md` | User-only editor. Rules copy `~/.claude/CLAUDE.md` "Answer style: KISS". |
| `skills/spec/SKILL.md:47` | Step 3 says "apply the `/ccs:kiss` rules" — the model can't load a user-only skill. |
| `skills/investigate`, `fix` | Have output/commit formats, no KISS rule. |
| `skills/break-it-down/STYLE.md` | Own register ("SIMPLE"); no KISS rule. |
| `skills/learn/SKILL.md` | 17 lines: "follow the harness memory rules". |
| Plugin source | `github.com/serhiileniv/ccs`, checkout at `~/Projects/ccs`. Installed copy lives in `~/.claude/plugins/cache/ccs/…` and is overwritten on update. |
| README, `plugin.json`, `marketplace.json` | List `kiss`; describe "session-to-memory learning". |

## Proposed design

### 1. Drop `kiss`, inline the rule

Delete `skills/kiss/`. Every skill that writes gets the same `## Writing` block:

```markdown
## Writing

One line where one line does. Answer first. State the fact, not the story behind it — no
narrating what was tried, no paragraph restating the line above.
Two things always stay: **why** a non-obvious decision was made, and **what a check cannot do**.
Caveats and risks still get said, one line each.
```

| Skill | Where |
|---|---|
| `spec` | End of file; step 3 points at it. `learn` treats this copy as canonical. |
| `investigate` | Above `## Output` |
| `fix` | Above step 5 (governs the commit body) |
| `learn` | Governs the edits it writes |
| `explain` | In `STYLE.md`, scoped to prose; the Part structure stays as is |

Inline copy, not a shared file: skills stay copyable one directory at a time (README
advertises that). Drift is `learn`'s job to catch.

### 2. `learn` rewritten

User-only (`disable-model-invocation: true`) — it edits plugin source.

```
/ccs:learn [what to learn — empty = review this session]
```

1. **Find lessons.** From the session: a user correction a ccs skill should have
   prevented, a step that was skipped or misread because its wording allowed it, a rule
   that proved wrong. Ignore project facts and one-off preferences.
2. **Map each to one skill.** No matching skill → report it, don't edit.
3. **Locate source.** `$CCS_REPO`, else `~/Projects/ccs`. Must be a git checkout whose
   `origin` is `serhiileniv/ccs`; otherwise print the proposed diff and stop. Never edit
   `~/.claude/plugins/cache`.
4. **Edit.** Smallest change to that `SKILL.md` (or its supporting file). Prefer tightening
   an existing line over adding one. Branch `learn/<slug>` off `main` if the tree is clean;
   a dirty tree → stop and say so.
5. **Drift.** If the `## Writing` blocks differ across skills, make them match the one in
   `spec` in the same diff.
6. **Gate.** Show the diff, one line per lesson: *what happened → what changed*. On
   approval, commit on the branch. Never push or open a PR.

Nothing qualified → say so, change nothing.

### 3. Rename `break-it-down` → `explain`

`git mv skills/break-it-down skills/explain`; `name: explain`, title and self-references
updated. Invoked as `/ccs:explain`. Output file names (`*-study-guide.html`) unchanged.

### 4. Docs

- README: remove `kiss` row; `learn` row → "Turns session lessons into edits to these skills."
- `plugin.json`, `marketplace.json` description: "Spec-first work, KISS output, and skills
  that learn from use." Drop `memory` keyword.
- README: `break-it-down` row and copy-by-hand example → `explain`.
- `docs/specs/plugin-framework.md`: mark the `kiss`/`learn` rows and Open question 4 as
  superseded by this spec.

## Acceptance criteria

- [ ] `skills/kiss/` is gone; `grep -r "ccs:kiss\|/kiss" skills README.md .claude-plugin` is empty
- [ ] `spec`, `investigate`, `fix`, `learn`, `explain/STYLE.md` each contain the same `## Writing` block
- [ ] `grep -rn break-it-down` outside `docs/specs/` is empty
- [ ] `learn` never writes under `~/.claude/projects/*/memory` or `~/.claude/plugins/cache`
- [ ] `learn` in a session with a skill correction shows a diff on `learn/<slug>`, commits only after approval, never pushes
- [ ] `learn` with no `~/Projects/ccs` and no `$CCS_REPO` prints a diff and edits nothing
- [ ] `learn` in a session with no skill-related lesson reports "nothing" and edits nothing

## Open questions

1. ~~Commit, push, or PR?~~ Settled: commit on approval, never push.
2. ~~Fix `## Writing` drift?~~ Settled: yes, in the same diff.
