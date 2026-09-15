# CCS — Claude Code Skills

Personal [Agent Skills](https://agentskills.io) for [Claude Code](https://code.claude.com/docs/en/skills.md).

Spec before code, KISS on everything written, and a session that leaves memory behind.

## Skills

| Skill | What it does |
|---|---|
| `/ccs:spec <slug>` | Writes a design spec to `docs/specs/<slug>.md`, then stops for approval. Refuses trivial work. |
| `/ccs:investigate <q>` | Read-only. How something works, or why it broke — with `file:line` evidence. |
| `/ccs:fix <what>` | Failing test first, watch it fail, smallest fix, full suite green. |
| `/ccs:kiss [path]` | Edits a draft, PR body, or commit message down to what it needs to be. User-invoked only. |
| `/ccs:learn` | Saves what the session established as memory files. User-invoked only. |
| [`break-it-down`](skills/break-it-down/SKILL.md) | Generates a standalone HTML study guide for finished work (PR, fix, feature) so you can defend it. Optional target and `lang: uk`. User-invoked only. |

## Guides

| Guide | What it is |
|---|---|
| [design](docs/guides/design.md) | Conventions for Solt Wagner–style Framer marketing sites. Plain markdown, not a skill: point any AI at it before building. |

## Install

```
/plugin marketplace add serhiileniv/ccs
/plugin install ccs@ccs
```

Restart Claude Code.

<details>
<summary>Or copy a single skill by hand</summary>

```bash
cp -r skills/break-it-down ~/.claude/skills/break-it-down
```

Or into one project: `<repo>/.claude/skills/break-it-down`. Restart, then type `/break-it-down`.

## Skill anatomy

A skill is a directory with a `SKILL.md` entry point (YAML frontmatter + instructions) plus
optional files loaded on demand:

```
skills/break-it-down/
├── SKILL.md        # frontmatter + the steps the agent follows
├── STYLE.md        # the study-guide style contract
└── example.html    # a finished guide; its <head> and CSS are the template
```

## Portability

`argument-hint` and `disable-model-invocation` are Claude Code extensions, not part
of the [Agent Skills](https://agentskills.io) spec. Uploading to claude.ai or packaging with
`package_skill.py` fails with `Unexpected key(s) in SKILL.md frontmatter` — drop those lines
first. The spec allows `name`, `description`, `license`, `compatibility`, `metadata` and
`allowed-tools`.

</details>
