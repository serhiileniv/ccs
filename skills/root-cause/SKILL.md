---
name: root-cause
description: Read-only. Find the root cause of a bug using every evidence source in reach (repo, CLIs, MCP servers for errors, logs, traces, analytics, DB). Use when something is broken and the cause is unknown.
argument-hint: "<symptom | error | ticket>"
---

# /ccs:root-cause

Find why it broke, from evidence. Check what you can read before you theorise.

## The hard rule

**Read-only everywhere.** No edits, no DB writes, no redeploys, no flag or config changes,
no ticket status changes. Read queries, log searches and tests that don't write are fine.
Never print secrets read along the way. The one write is this run's report.

The answer implies a change → name it and stop. The fix is `/ccs:implement`.

## Step 1 — pin the symptom

One line each: expected, actual, since when, where (prod / preview / local), who (all
users or some). Missing and not findable → ask.

## Step 2 — inventory

List what you can read **now**. Check it; never assume it:

- repo and git
- CLIs: `gh`, `vercel`, `railway`, `supabase`: `command -v`, then a whoami
- MCP servers: every tool in this session, deferred ones included, plus `claude mcp list`

Keep what fits the symptom:

| Source | Answers | Example tools |
|---|---|---|
| Repo, git | What changed, when, by whom | `git log -S`, `git bisect`, `git blame`, tests |
| Error tracking | Stack, first seen, release, frequency, users hit | Sentry, Datadog error tracking |
| Logs, traces | What happened around one request | Datadog logs/spans, Vercel runtime logs, `railway logs`, Supabase logs |
| Deploys, config | What shipped and when | `gh`, Vercel deployments, `railway variables` |
| Product analytics | Which users, which path, session replay | PostHog |
| Database | Actual state of the data | Supabase `execute_sql`, SELECT only |
| Tickets | The report, repro steps, related reports | Linear, Jira, ClickUp |

A relevant source is connected → query it before stating any hypothesis.

## Step 3 — scope check

A connected source may point at another org or project. Confirm it matches this repo
(project name, service, DSN, env) before trusting it. Some MCPs don't expose their
scope; say so when you rely on one.

## Step 4 — reproduce

Locally, or pin one concrete prod instance: event id, trace id, request, user. Neither →
say so; everything after is inference.

## Step 5 — strategy

| Symptom shape | Strategy |
|---|---|
| Started at a known time | Line it up with deploys, commits, config changes; `git bisect` last good → first bad |
| Has a stack trace | Error tracker: first-seen release, then follow the stack into the code |
| Only some users or inputs | Diff failing against passing: attributes, inputs, env, version |
| Wrong data | Trace back from the bad row to the code that wrote it |
| Intermittent | Timing, concurrency, retries, caches; correlate with traces |
| Slow | Traces and spans, then DB query performance |
| None of the above | Halve: input, config, code path, time range |

Write 2–3 competing hypotheses, each with the check that would kill it. Run the cheapest
check that separates them first. Cite `file:line` for every claim; mark inference as
inference.

## Step 6 — root cause

The thing that, changed, makes the symptom impossible. Not the line that threw. Keep
asking "why" until you reach something code or config can change. Reached only a
proximate cause → say so.

## Output

```
Symptom: <one line>

Outcome: found | proximate only | not found

Root cause: <first, before any detail>

Evidence:
  - file.ts:42 — <what it proves>
  - <source>: <event / trace / query result that decided it>

Sources: <checked and used> · not available: <relevant but missing, and what it would have answered>

Not verified: <what could not be confirmed, and why>

Fix: /ccs:implement <one line; the reproduction test to write first>
```

No retelling of the search. Mention a discarded theory only if it is still live.

## Writing

One line where one line does. Answer first. State the fact, not the story behind it — no
narrating what was tried, no paragraph restating the line above.
Two things always stay: **why** a non-obvious decision was made, and **what a check cannot do**.
Caveats and risks still get said, one line each.

Outcome: success = root cause found · partial = proximate only · failed = not found.

## Report

Last step, every run, whatever the outcome: write `.ccs/reports/<skill>/<YYYY-MM-DD>-<slug>.md`
at the repo root, and add `.ccs/` to `.git/info/exclude` if missing. One line per field,
no secrets, tokens or customer data. Print the path.

```
---
skill: <skill>
date: <YYYY-MM-DD>
repo: <owner/name>
target: <argument, one line>
outcome: success | partial | failed
learned: false
---

Used: <tools, CLIs, MCPs, files that did the work>
Found: <the result, one or two lines>
Friction: <what slowed or blocked it; a user correction; "none">
```
