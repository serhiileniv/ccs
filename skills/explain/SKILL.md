---
name: explain
description: Generate a standalone HTML study guide explaining a finished piece of work (bug fix, PR, feature) so the user can fully own it — context first, concept before details, guided code reading, interview-ready summary.
argument-hint: "[what to explain — defaults to this session's main work] [lang: en|uk]"
disable-model-invocation: true
allowed-tools: Read Write Glob Grep Bash(git diff:*) Bash(git show:*) Bash(git log:*) Bash(git status:*) Bash(gh pr view:*) Bash(gh pr diff:*) Bash(gh issue view:*)
---

# /ccs:explain — study-guide generator

Produce a standalone HTML study guide that lets the user *own* a piece of work:
explain it to a reviewer, defend it in an interview, or re-load it into their own
head months later.

## 1. Resolve scope

- If `$ARGUMENTS` names a target (PR URL, issue number, commit range, file, or a
  topic like "the openclaw memory fix"), that is the subject.
- Language: write in Ukrainian (`<html lang="uk">`) only if `$ARGUMENTS` carries an
  explicit `lang: uk` or a standalone `uk` token. A `uk` inside a word (`duke`,
  `uk-locale`) is part of the subject, not a language flag. Otherwise write in English.
- With no arguments: the subject is the main piece of work completed in this
  session. If the session contains no substantive work, ask the user what to
  explain — do not invent a subject.

## 2. Gather facts before writing

Never explain from memory alone. Read the actual final state:

- the diff / final code (`git diff`, `git show`, Read on the touched files),
- the PR and issue text when a PR is involved (`gh pr view`, `gh issue view`),
- the tests that lock the behavior,
- review feedback that shaped the result (it often contains the best "twist" story).

If explaining someone else's code rather than a change, read the entry points and
callers before writing anything.

## 3. Write the guide

- Structure and voice: follow [STYLE.md](STYLE.md) exactly — the fixed Part
  order (know-first concepts → intuition → the idea → the twist → the code →
  glossary → defend-it), the signaling rules (lead lines, sticky Part nav,
  boxes), "Plain English:" translations, and the self-check questions.
- Start from [example.html](example.html) — a finished guide built to this
  contract. Keep its `<head>` and CSS verbatim, replace the body. Copy its shape,
  length and register; ignore its subject. One self-contained file (inline CSS,
  no external requests).
- File name: `<short-topic>-study-guide.html` (kebab-case topic).
- Location. The test is: could this file end up in a diff the user sends
  upstream? If yes, it goes elsewhere. Resolve in order:
  1. Beside existing `*-study-guide.html` files, if the user keeps any — that is
     already their shelf.
  2. Else, if the explained code lives in a repo the user contributes to rather
     than owns (a fork, an upstream remote, an open PR), write to a
     `study-guides/` directory beside that repo root — outside its working tree.
  3. Else (the user's own project), the project root is fine.
  Never `git add` the guide, and never write it into a checkout with staged or
  in-flight PR work.
- Default to the SIMPLE register defined in STYLE.md. If the user later says
  "simplify", rewrite the SAME file simpler — don't create a second file.

## 4. Close the loop

After writing, tell the user the file path and give a one-paragraph map of the
guide's sections. Offer to simplify further or produce the other language on
request.
