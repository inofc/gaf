# 🎩 GAF — Generative AI Factory

**GAF is a pattern; a Factory is one installation of it.** It turns a software
project into one that improves itself: the community proposes ideas, votes on
them, and independently scheduled AI roles review, build, and ship the winners
— each decision documented in public, the whole pipeline steered by one
owner-controlled rulebook.

This repository is the **installable skill** that sets a Factory up in any
project: a game, a SaaS product, a website builder, an internal tool.

## Provenance

This is not speculative documentation. It was reverse-standardized from a
live, working Factory — a browser game whose community ideas were triaged,
built, merged, and deployed by AI roles with no human in the loop, across
multiple shipped versions. Every design rule here exists because of a real
success or a real failure in that installation.

## What's in here

| Path | What it is |
| ---- | ---------- |
| [`SKILL.md`](./SKILL.md) | **Start here.** The setup protocol — 6 phases, every step tagged `[AGENT]`, `[OPERATOR]`, or `[TOGETHER]`. |
| [`references/BLUEPRINT.md`](./references/BLUEPRINT.md) | The invariant pattern: data model, label grammar, meta block, web-layer contract, and the design rules that keep a Factory alive unattended. |
| [`references/ROLES.md`](./references/ROLES.md) | The six-clause role contract, the default Architect → Coder pipeline, and recipes for extending it (Tester, Deployer). |
| [`references/OWNER_WORKBOOK.md`](./references/OWNER_WORKBOOK.md) | The guided owner interview — 12 decisions, each explained and illustrated across four domains before it's asked. |
| [`references/OPERATOR_GUIDE.md`](./references/OPERATOR_GUIDE.md) | The human-only steps: bot account, tokens, secrets, schedules — with the traps that were hit for real. |
| [`references/ADAPTATION.md`](./references/ADAPTATION.md) | Named swap points: issue tracker, deploy target, email provider, runner, and the hobby → business → regulated posture ladder. |
| [`templates/`](./templates) | Fill-in templates for every generated file, plus the default label set as JSON. |
| [`WEAKNESSES.md`](./WEAKNESSES.md) | Known limitations of the pattern (K1–K10), deliberately deferred. Read before installing; revisit as a Factory grows. |

## Installing it in a project

Copy this repository's contents into the target project as a skill, then run it:

```bash
mkdir -p .claude/skills/gaf-setup
# copy this repo's files into that folder (git clone, or cp from a local checkout)
```

The folder must be named **`gaf-setup`** to match the `name:` in `SKILL.md`'s
frontmatter. Then, in a session on that project:

> *"Set up a GAF in this project — read `.claude/skills/gaf-setup/SKILL.md`
> and follow it."*

## What setup actually requires

A Factory cannot be installed by an AI alone, by design. The agent handles
discovery, file generation, and the smoke test; **a human owner is required**
for two things no automation should decide:

- **The Owner's Workbook session** (~30–45 min) — what the product is, what
  may never be broken, what "shipped" means, how fast the roles run. These
  answers become the rulebook that constrains an otherwise unsupervised
  pipeline.
- **Credentials and schedules** — the bot account, its token, the hosting
  secrets, and the schedules that start the Factory.

Have the bot account and its token ready before the session and setup goes
faster; `references/OPERATOR_GUIDE.md` lists exactly what to prepare.

## How it works, in one diagram

```
User submits idea (Ideas Board or issue tracker)
   → Issue            labels: idea, stage:submitted          ── community votes
   → AI Architect     (scheduled; one idea per run)
                        approve → architect:approved + impact:* + coder:queued
                        decline → architect:declined
                        + mandatory "Architect Assessment" comment
   → AI Coder         (separately scheduled; one idea per run)
                        coder:queued → coder:in-process → coder:executed
                        implements on a factory/* branch → PR → ships
                        + mandatory "Implementation Report" comment
   → new version live per the installation's ship definition
   → submitter notified at each stage (optional)
```

Three commitments make it work: **the issue tracker is the entire database**
(labels are the state machine, no hidden state), **role instructions live in
the repo as files** (so behavior changes are file edits, never schedule
rewiring, and any runner or model can execute a role), and **nothing happens
without a public paper trail** (no verdict without an assessment, no ship
without a report, every approved idea in a ledger).
