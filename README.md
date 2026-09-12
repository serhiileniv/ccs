# CCS — Claude Code Skills

Personal commands and [Agent Skills](https://agentskills.io) for [Claude Code](https://code.claude.com/docs/en/skills.md).

Spec before code, KISS on everything written, and a session that leaves memory behind.
Composes with [ECC](https://github.com/affaan-m/ECC) rather than replacing it.

## Skills

| Skill | Command | What it does |
|-------|---------|--------------|
| [break-it-down](skills/break-it-down/SKILL.md) | `/break-it-down` | Generates a standalone HTML **study guide** for a finished piece of work (bug fix, PR, feature) so you can fully own it. Seven parts, built on learning science: the concepts and system map you need *before* the story, context told through one analogy, the fix in one sentence before any detail, the hardest subtlety as a story, guided code reading with plain-English translations, a mini-glossary, and a "defend it" part — self-check questions with hidden answers, likely reviewer challenges, and a speakable 30-second interview answer. User-invocable only (`disable-model-invocation: true`). Arguments: an optional target (PR URL, commit range, topic — defaults to the session's main work) and optional `lang: uk` for a Ukrainian guide. |

## Commands

Installed with the plugin (see below):

| Command | What it does |
|---|---|
| `/ccs:spec <slug>` | Writes a design spec to `docs/specs/<slug>.md`, then stops for approval. Refuses trivial work. |
| `/ccs:investigate <q>` | Read-only. How something works, or why it broke — with `file:line` evidence. Changes nothing. |
| `/ccs:fix <what>` | Failing test first, watch it fail, smallest fix, full suite green. |
| `/ccs:learn` | Distils the session into durable memory files, one fact each. |
| `/ccs:kiss [path]` | Edits a draft, PR body, or commit message down to what it needs to be. |

## Guides

| Guide | What it is |
|-------|------------|
| [design](skills/design/design.md) | Conventions for building good-looking responsive entertaining marketing sites in Solt Wagner Framer style (screen.movie, supaste, revone, runey, frameblox, dock.cool) — free with Framer free tier or Astro + Tailwind. Plain markdown, not a skill: point any AI at it before building. |

## Install

```
/plugin marketplace add serhiileniv/ccs
/plugin install ccs@ccs
```

Restart Claude Code. Commands resolve as `/ccs:*`; `/break-it-down` keeps its bare name.

<details>
<summary>Or copy a single skill by hand</summary>

Copy a skill into your user-level skills directory:

```bash
# Windows
xcopy /E /I skills\break-it-down "%USERPROFILE%\.claude\skills\break-it-down"

# macOS / Linux
cp -r skills/break-it-down ~/.claude/skills/break-it-down
```

Or into a single project: copy to `<repo>/.claude/skills/break-it-down`.

Restart your Claude Code session, then type `/break-it-down`.

## Skill anatomy

Each skill is a directory with a `SKILL.md` entry point (YAML frontmatter + imperative
instructions) plus optional supporting files loaded on demand:

```
skills/break-it-down/
├── SKILL.md        # frontmatter + the four steps the agent follows
├── STYLE.md        # the study-guide style contract (Parts 0–6, signaling, simplicity rules)
├── template.html   # self-contained HTML skeleton every guide starts from (light + dark)
└── example.html    # a finished guide built to the contract — the shape to match
```

## Portability

`argument-hint` and `disable-model-invocation` are Claude Code extensions, not part
of the [Agent Skills](https://agentskills.io) spec. They work at every Claude Code
skill level, but uploading to claude.ai or packaging with `package_skill.py` fails
with a hard `Unexpected key(s) in SKILL.md frontmatter` error — drop those two lines
first. The spec allows `name`, `description`, `license`, `compatibility`,
`metadata` and `allowed-tools`.

</details>
