# The study-guide style contract

Purpose: after reading, the user fully understands the context, then the change,
and can DEFEND it — to a reviewer, an interviewer, or their future self.
The structure follows learning science: pre-train concepts first (advance
organizer), segment into small parts, signal what matters, and end with
retrieval practice.

## Part order (fixed)

**Part 0 — What you need to know first.** The prerequisite knowledge, taught
BEFORE any story (Mayer's pre-training principle). Two pieces:
- *Concept cards*: 3–6 cards, one per codebase/domain concept the reader must
  hold (term → one-line meaning → why it matters here). Only concepts actually
  used later.
- *The system map*: a tiny CSS diagram (example.html has `.diagram`) showing where
  the touched code sits — what calls it, what it owns.

**Part 1 — The intuition.** Three signposted blocks, in this order:
- **What we HAVE** — the current system, explained through ONE everyday analogy
  (library catalog, thermostat, postal service…). One analogy for the whole
  document; never mix.
- **What we WANT** — the desired behavior, one short paragraph.
- **What's IN THE WAY** — the problem as a NUMBERED STORY of steps a user
  actually lived, ending with a red box showing the user-visible damage. For a
  feature or refactor with no failure story, this is the cost of the gap
  instead: the workaround people run today, or the thing they simply cannot do.
  Concrete and lived either way — never invent damage nobody suffered.
Also state the single load-bearing fact the whole solution depends on, in a
highlighted box (e.g. "the index is derived data — the files are the truth").

**Part 2 — The idea of the change** ("of the fix" for bug work; use the word the
work deserves). The change in ONE sentence in a green box. Then
"why that's right" as 2–4 bullets. Then the key distinction the design rests on
as a small table (e.g. resolvable divergence vs must-abort failure).

**Part 3 — The twist.** The hardest subtlety as a story: the naive version →
red "why that's poisonous" box with the concrete bad outcome → green "the rule"
box with the repair. If a reviewer caught the flaw, credit them honestly — it
makes the defense stronger; if no review shaped the work, don't invent one.
If the work has no twist, say exactly that in one line — "the obvious version is
the shipped version, and here is why nothing subtler was needed" — and go on to
Part 4. That sentence is itself a defensible answer.

**Part 4 — The details: reading the code.** 3–5 blocks of FINAL shipped code
(never pseudocode), each followed by a **"Plain English:"** one-liner. Annotate
load-bearing lines inside the code ("← this WHERE clause is the Part-3 repair").
Explain ordering constraints (why X must precede Y). Narrate tests as
arrange / act / assert; mention the evidence trick if used (new test fails on
unpatched code with the exact production error). Close with "what we
deliberately did NOT touch" and why.

**Part 5 — Glossary.** Collapsible `<details>` entries; ONLY terms used above;
1–2 plain sentences each.

**Part 6 — Defend it.** Retrieval practice — this is what converts reading into
ownership:
- *Check yourself*: 4–6 questions with answers hidden in `<details>` ("Why is
  dropping diverged rows not data loss?"). The reader should attempt each
  before revealing.
- *Likely challenges*: 3–5 hard reviewer/interviewer questions with strong
  short answers.
- *The 30-second version*: one quoted paragraph, speakable verbatim:
  problem → policy → subtlety → proof.

**Footer** (optional): live status of the PR/work in one line.

## When a part has nothing honest to say

The order is fixed; the content is not guaranteed to exist. Parts 1 and 3 assume
a bug shape — a lived failure, a poisonous naive fix — and plenty of good work
has neither. When a part has nothing true in it, say so in one line and move on.
Never manufacture a failure story, a twist, or a reviewer to fill the shape:
an invented one collapses under the first question, which is the exact moment
the guide exists to survive.

## Signaling rules (apply throughout)

- Every Part heading is followed by a one-line `.lead` — "In this part: …" —
  saying what the reader will be able to do after it.
- A sticky Part nav lists the Parts (the advance organizer, always visible): a sidebar
  with reading progress on desktop, a top bar on phones.
- Boxes are signals, not decoration: `.box.good` = the rule/the fix,
  `.box.bad` = the failure mode, `.box` = the load-bearing fact / status.
  The status footer follows the same rule — green only if the work actually
  landed, `.box.bad` if it is blocked or failing.
- `.badge` chips belong in the hero subtitle only (repo, PR number, state).
  Never use them as body decoration.
- Tables for any either/or comparison; numbered lists for any sequence.

## Writing

One line where one line does. Answer first. State the fact, not the story behind it — no
narrating what was tried, no paragraph restating the line above.
Two things always stay: **why** a non-obvious decision was made, and **what a check cannot do**.
Caveats and risks still get said, one line each.

## Simplicity rules (the default register)

In a guide, **Writing** governs sentences, not structure: the Part order above still holds.


- Short sentences. One idea per paragraph. Cut anything that doesn't change
  what the reader would say or do (coherence principle).
- Analogy over jargon; jargon only if the glossary defines it.
- Every code block earns its "Plain English:" line — no exceptions.
- Depth goes into collapsibles, not the main path.
- If a section can't be explained simply, the understanding isn't done —
  go back to the code.
- Budget: roughly 1,200–2,000 words of prose outside the code blocks — about a
  ten-minute read. Over budget means cutting, not a second file; a guide nobody
  finishes teaches nothing. (Collapsible counts are set by Parts 5 and 6.)

## The reference rendering

[example.html](example.html) is a complete guide built to this contract, on a
worked example. Read it before writing — it settles questions of length, tone,
and box use faster than this file can. Match its shape, not its subject.

## Visual/typographic rules (already implemented in example.html)

- Text measure ≈ 70ch; body ≥ 16px; line-height ≥ 1.6.
- Light theme by default, dark via `prefers-color-scheme` — soft contrast in
  both (no pure white on pure black).
- System sans-serif stack; monospace only for code.
- Code coloring spans: `.k` keyword, `.s` string, `.c` comment, `.f` function.
