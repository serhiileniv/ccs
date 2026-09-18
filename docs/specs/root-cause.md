# Spec: `/ccs:root-cause` — find why a bug happens, with every source in reach

## Goal

One read-only skill finds the root cause of a bug. Before it theorises, it takes stock of
what it can actually read: repo, CLIs, connected MCP servers (errors, logs, traces,
analytics, DB). Then it picks the strategy that fits the symptom. It replaces
`/ccs:investigate` and hands its result to `/ccs:implement`.

## Non-goals

- **Fixing.** `/ccs:implement` owns code changes. This skill changes nothing.
- **"How does this work" questions.** `investigate` answered them; nothing will after this. Ask directly.
- **Monitoring or alerting setup.** A missing source gets reported, not installed.

## Current state

| | |
|---|---|
| `skills/investigate/SKILL.md` | Read-only; how-it-works and why-it-broke. Reproduce, cite `file:line`, halve. Uses only repo and git; no CLI or MCP sources. |
| `skills/implement/SKILL.md` step 0 | Unknown cause → `/ccs:investigate`. |
| `README.md` | Lists `/ccs:investigate`. |
| `docs/specs/implement.md` | Routes unknown causes to `investigate`. |
| `~/.claude/CLAUDE.md` "Check your own access" | CLIs → credentials → MCP → vendor APIs; "read production" when claiming its state. Machine-only, not in the plugin. |

## Proposed design

### Skill

`skills/root-cause/SKILL.md`, model-invocable. `skills/investigate/` deleted.

```yaml
name: root-cause
description: Read-only. Find the root cause of a bug using every evidence source in reach (repo, CLIs, MCP servers for errors, logs, traces, analytics, DB). Use when something is broken and the cause is unknown.
argument-hint: "<symptom | error | ticket>"
```

### The hard rule

Read-only everywhere. No edits, no DB writes, no redeploys, no flag or config changes, no
ticket status changes. Read queries, log searches and non-writing tests are fine. Never
print secrets read along the way. The one write is its own report in `.ccs/reports/`, excluded from git.

### Steps

| # | Step | Rule |
|---|---|---|
| 1 | Pin the symptom | One line each: expected, actual, since when, where (prod / preview / local), who (all users or some). Missing and not findable → ask. |
| 2 | Inventory | List what can be read **now**, checked rather than assumed: repo + git; CLIs (`gh`, `vercel`, `railway`, `supabase`; `command -v` + a whoami); MCP servers from the session's tools, deferred ones included, and `claude mcp list`. Keep the ones relevant to the symptom, in the table below. |
| 3 | Scope check | A connected source may point at another org or project. Confirm it matches this repo (project name, service, DSN, env) before trusting it. |
| 4 | Reproduce | Locally, or pin a concrete prod instance (event id, trace id, request, user). Neither → say so; the rest is then inference. |
| 5 | Pick a strategy | By symptom shape, table below. Write 2–3 competing hypotheses, each with the check that would kill it. Run the cheapest check that separates them first. |
| 6 | Root cause | The thing that, changed, makes the symptom impossible, not the line that threw. Keep asking "why" until you reach something code or config can change. Only a proximate cause reached → say so. |

### Sources (step 2)

| Source | Answers | Example tools |
|---|---|---|
| Repo, git | What changed, when, by whom | `git log -S`, `git bisect`, `git blame`, tests |
| Error tracking | Stack, first seen, release, frequency, users hit | Sentry, Datadog error tracking |
| Logs, traces | What happened around one request | Datadog logs/spans, Vercel runtime logs, `railway logs`, Supabase logs |
| Deploys, config | What shipped and when | `gh`, Vercel deployments, `railway variables` |
| Product analytics | Which users, which path, session replay | PostHog |
| Database | Actual state of the data | Supabase `execute_sql` (SELECT only) |
| Tickets | The report, repro steps, related reports | Linear, Jira, ClickUp |

### Strategies (step 5)

| Symptom shape | Strategy |
|---|---|
| Started at a known time | Line it up with deploys, commits, config changes; `git bisect` last good → first bad |
| Has a stack trace | Error tracker: first-seen release, then follow the stack into the code |
| Only some users or inputs | Diff failing against passing cases: attributes, inputs, env, version |
| Wrong data | Trace backwards from the bad row to the code that wrote it |
| Intermittent | Timing, concurrency, retries, caches; correlate with traces |
| Slow | Traces and spans, then DB query performance |
| None of the above | Halve: input, config, code path, time range |

### Output

```
Symptom: <one line>

Outcome: found | proximate only | not found

Root cause: <first, before any detail; "proximate only" if so>

Evidence:
  - file.ts:42 — <what it proves>
  - <source>: <event / trace / query result that decided it>

Sources: <checked and used> · not available: <relevant but missing, and what it would have answered>

Not verified: <what could not be confirmed, and why>

Fix: /ccs:implement <one line; the reproduction test to write first>
```

The same content is saved as this run's report ([skill-reports](skill-reports.md)). Here `outcome` is
`found`, `proximate only` or `not found`, `used` is the Sources line, and `found` is the root cause.

Ends with the shared `## Writing` and `## Report` blocks.

### Wiring

| File | Change |
|---|---|
| `skills/investigate/` | Deleted |
| `skills/implement/SKILL.md` step 0 | `/ccs:investigate` → `/ccs:root-cause` |
| `docs/specs/implement.md` | Same rename |
| `README.md` | `investigate` row → `/ccs:root-cause <symptom>`: "Read-only. Checks every source in reach (repo, CLIs, MCPs), then finds the root cause with evidence." |
| `docs/specs/plugin-framework.md` | Header note: `investigate` superseded by [root-cause](root-cause.md) |
| `plugin.json` | Stays `0.3.0`: unreleased, already bumped for `implement` |

## Acceptance criteria

- [ ] `skills/root-cause/SKILL.md` exists with the frontmatter above; `skills/investigate/` is gone
- [ ] `grep -rn "ccs:investigate" skills README.md .claude-plugin` is empty
- [ ] Its `## Writing` and `## Report` blocks match `skills/spec/SKILL.md` exactly
- [ ] On a real bug, output has every section of the Output block, `Sources` names what was checked, and a report file is written
- [ ] With Sentry or Datadog connected and relevant, it is queried before any hypothesis is stated
- [ ] No write happens: no file edit, DB write, deploy or ticket change in the transcript
- [ ] Handoff line runs as-is: `/ccs:implement` accepts it

Criteria 4–7 describe behaviour, checked by one run on a real bug; grep can't check them.
Step 3's scope check lowers the risk of reading the wrong project but can't remove it:
some MCPs don't expose which project they point at.

## Open questions

1. ~~Name?~~ Settled: `root-cause`.
2. ~~Keep how-it-works questions?~~ Settled: dropped; the skill stays single-purpose.
