---
description: Distil the session into durable memory files — one fact per file, indexed in MEMORY.md.
argument-hint: "[what to remember]"
---

# /sl:learn

Turn what this session established into memory that survives it.

## Where it goes

```
~/.claude/projects/<slug>/memory/
```

`<slug>` is the working directory path with every `/` replaced by `-`
(`/Users/x/Projects` → `-Users-x-Projects`). Create the directory if missing.

## What qualifies

Save only what is **non-obvious and durable**:

| Type | What it holds |
|---|---|
| `user` | Who the user is — role, expertise, standing preferences |
| `feedback` | Guidance on how to work: a correction, or an approach they confirmed. Include the why. |
| `project` | Ongoing work, goals, constraints not derivable from the code. Absolute dates, never "last week". |
| `reference` | Pointers out — URLs, dashboards, tickets |

**Do not save** what the repo already records: code structure, past fixes, git history,
anything in CLAUDE.md. Do not save what only mattered inside this conversation.

If the user asks to remember something the repo already holds, ask what was non-obvious
about it and save that instead.

## Format — one fact per file

```markdown
---
name: <short-kebab-case-slug>
description: <one line; this is what decides relevance at recall time>
metadata:
  type: user | feedback | project | reference
---

<The fact, stated plainly.>

<For feedback and project, follow with:>
**Why:** <the reason it matters>
**How to apply:** <what to do differently>
```

Link related memories with `[[their-name-slug]]`. Link liberally — a `[[link]]` with no
file behind it yet is fine, it marks something worth writing later.

## Before writing — check for a duplicate

Read the existing files. If one already covers this, **update that file** rather than
adding a near-copy. Delete memories that turned out to be wrong.

## After writing — index it

Append one line to `MEMORY.md`:

```
- [Title](file.md) — hook
```

`MEMORY.md` is the index loaded every session. One line per memory, no frontmatter,
never the content itself.

## KISS

The description line decides whether the memory is ever recalled. Make it say what the
fact *is*, not what topic it is about.

Report what was written, in one line each. If nothing qualified, say that — an empty
`/sl:learn` is a valid outcome and better than filler.
