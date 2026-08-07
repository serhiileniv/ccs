# skills

Personal [Agent Skills](https://agentskills.io) for [Claude Code](https://code.claude.com/docs/en/skills.md).

## Skills

| Skill | Command | What it does |
|-------|---------|--------------|
| [own-this](skills/own-this/SKILL.md) | `/own-this` | Generates a standalone HTML **study guide** for a finished piece of work (bug fix, PR, feature) so you can fully own it. Seven parts, built on learning science: the concepts and system map you need *before* the story, context told through one analogy, the fix in one sentence before any detail, the hardest subtlety as a story, guided code reading with plain-English translations, a mini-glossary, and a "defend it" part — self-check questions with hidden answers, likely reviewer challenges, and a speakable 30-second interview answer. User-invocable only (`disable-model-invocation: true`). Arguments: an optional target (PR URL, commit range, topic — defaults to the session's main work) and optional `lang: uk` for a Ukrainian guide. |

## Install

Copy a skill into your user-level skills directory:

```bash
# Windows
xcopy /E /I skills\own-this "%USERPROFILE%\.claude\skills\own-this"

# macOS / Linux
cp -r skills/own-this ~/.claude/skills/own-this
```

Or into a single project: copy to `<repo>/.claude/skills/own-this`.

Restart your Claude Code session, then type `/own-this`.

## Skill anatomy

Each skill is a directory with a `SKILL.md` entry point (YAML frontmatter + imperative
instructions) plus optional supporting files loaded on demand:

```
skills/own-this/
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
