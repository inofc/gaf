---
name: gaf-setup
description: >
  Set up a GAF (Generative AI Factory) in any software project: an autonomous
  pipeline where a community proposes ideas, votes on them, and scheduled AI
  roles review, build, and ship the winners. Use this skill when a project
  owner asks to install, replicate, or bootstrap a GAF / AI Factory in their
  repository. Requires a human operator working alongside the AI agent — some
  steps (accounts, tokens, secrets, schedules) can only be done by a person.
---

# 🏰 GAF — Generative AI Factory: Setup Skill

**GAF** is the pattern; a **Factory** is one installation of it. This skill
turns any software project — a game, a SaaS product, a website builder, an
internal tool — into a project with a Factory: community ideas flow in, an
**AI Architect** triages them in public, an **AI Coder** builds and ships the
approved ones, and every decision leaves a documented paper trail. The
reference installation (Knight Duel, a browser game) shipped six community
ideas end-to-end with no human in the loop; this skill is that system,
reverse-standardized.

**Who executes what.** Every step below is tagged:

| Tag | Meaning |
| --- | ------- |
| **[AGENT]** | The AI agent does this alone. |
| **[OPERATOR]** | Only the human can do this (accounts, tokens, secrets, dashboards, schedules). The agent explains, waits, then verifies. |
| **[TOGETHER]** | A guided conversation: the agent teaches and asks, the human decides, the agent records. |

**Reading order for the agent:** this file end-to-end first, then
`references/BLUEPRINT.md` (the pattern), then `references/ROLES.md` (the role
contract). Open the other references when their phase arrives. Do not start
Phase 3 until the profile from Phase 1 is complete and the prerequisites from
Phase 2 are verified.

> Known limitations of the pattern (vote integrity, test gates, prompt-injection
> hardening, regulated-domain sign-off, …) are tracked separately in
> [`WEAKNESSES.md`](./WEAKNESSES.md). Read it once before installing so you can
> answer owner questions honestly; addressing the items is not part of setup.

---

## Phase 0 — Orient **[AGENT]**

Goal: know the pattern and know the target project before asking the human
anything.

1. Read [`references/BLUEPRINT.md`](./references/BLUEPRINT.md) — the invariant
   GAF pattern: data model, label grammar, meta block, design rules.
2. Read [`references/ROLES.md`](./references/ROLES.md) — the role contract and
   the default Architect → Coder pipeline.
3. Inspect the target repository and record (you will pre-fill the profile
   with these):
   - Stack, framework, package manager, and manifest file (where the version
     number lives).
   - The project's real verify commands (lint / build / tests) — run them once
     to confirm they pass **before** the Factory exists, so a later failure is
     attributable.
   - How the project deploys today (auto-deploy on push? CI pipeline? manual?).
   - The default branch name.
   - Whether the repo is public or private (drives the privacy posture in the
     workbook).
4. Copy [`templates/FACTORY_PROFILE.template.md`](./templates/FACTORY_PROFILE.template.md)
   to `ai-factory/FACTORY_PROFILE.md` in the target project and pre-fill every
   field you could determine yourself. Leave owner-decision fields empty —
   they are Phase 1's job.

## Phase 1 — Owner's Workbook session **[TOGETHER]**

Goal: every project-specific decision captured in `ai-factory/FACTORY_PROFILE.md`.

1. Open [`references/OWNER_WORKBOOK.md`](./references/OWNER_WORKBOOK.md). It
   contains one block per decision, each with an explanation of **what** is
   being asked, **why** the Factory needs it, **how to think about it**, and
   **worked examples from four domains** (game, SaaS, website builder,
   regulated/banking).
2. Walk the owner through the blocks **in order, in one session**, presenting
   the explanation and examples before asking each question. Do not skip the
   teaching part — a wrong answer to W2 (protected invariants) is the single
   most expensive mistake an installation can make.
3. Record each answer in the profile as you go. Where the owner is unsure,
   record the conservative default noted in the workbook block and flag it
   `(default — revisit)`.
4. Read the finished profile back to the owner as a summary and get an
   explicit "yes, that's my project" before proceeding.

**Output:** a complete `ai-factory/FACTORY_PROFILE.md` with no empty fields.

## Phase 2 — Prerequisites **[OPERATOR]**, verified **[AGENT]**

Goal: the credentials and accounts only a human can create, each one verified
by the agent before use.

1. Hand the owner [`references/OPERATOR_GUIDE.md`](./references/OPERATOR_GUIDE.md)
   and walk them through it. The steps (summarized — the guide has the
   details and the troubleshooting table):
   - **O1** Create the dedicated Factory **bot account** on the code platform.
   - **O2** Grant it **Write** (not admin) collaborator access; accept the invite.
   - **O3** Generate the bot's access token — on GitHub this must be a
     **classic PAT with `repo` scope** (fine-grained tokens 404 on collaborator
     repos owned by another personal account).
   - **O4** *(if web board)* Configure the hosting project and set the
     environment variables; **redeploy after setting them**.
   - **O5** *(if email notifications)* Create the email-provider account,
     verify the sender, generate the API key.
2. **Verify each item as it lands [AGENT]:**
   - Token reaches the repo:
     `curl -s -H "Authorization: Bearer <TOKEN>" https://api.github.com/repos/<owner>/<repo>` →
     expect JSON with `"id"`, not `Not Found` / `Bad credentials`.
   - Bot's collaborator invite is **accepted** (a pending invite behaves like
     no access).
   - *(web layer)* the deployed API answers without the "server not
     configured" error.
3. Do not proceed to Phase 3 on a promise — only on a passed check.

## Phase 3 — Build **[AGENT]**

Goal: the Factory exists in the repo, fully configured from the profile.
Everything in this phase is mechanical: **templates + profile = installation**.
Take placeholder values from `FACTORY_PROFILE.md`; if a needed value is
missing, go back to the owner rather than inventing one.

1. **Create the labels** from
   [`templates/labels.json`](./templates/labels.json) via the platform API.
   The default set covers the Architect → Coder pipeline; if the profile's
   pipeline shape (W8) adds roles, generate their three labels each
   (`<role>:queued|in-process|executed`) per the grammar in `ROLES.md`.
   Missing labels are the most common silent setup failure — create all of
   them now, don't rely on lazy creation.
2. **Instantiate the `ai-factory/` folder** in the target repo from
   [`templates/`](./templates):

   | Template | Becomes | Filled from |
   | -------- | ------- | ----------- |
   | `RULES.template.md` | `ai-factory/RULES.md` | W2–W5 (invariants, scope, content, privacy) |
   | `ARCHITECT.template.md` | `ai-factory/ARCHITECT.md` | stack facts, file map, W2 refs |
   | `CODER.template.md` | `ai-factory/CODER.md` | verify commands (W6), ship definition (W7) |
   | `CASES.template.md` | `ai-factory/CASES.md` | project name only |
   | `FACTORY_README.template.md` | `ai-factory/README.md` | pipeline shape, labels, env vars, cadence |

   Rules for instantiation: replace **every** `{PLACEHOLDER}`; delete the
   `<!-- template-note -->` blocks; the result must pass the **cold-read
   test** — an agent with zero context beyond that one file can execute the
   role. Re-read each generated file pretending you know nothing; if you'd
   have a question, the file failed.
3. **(If the profile says web board, W9)** implement the serverless API and
   board page per `BLUEPRINT.md` §5 in the project's own stack and visual
   style, and the in-product call-to-action links. Platform substitutions are
   in [`references/ADAPTATION.md`](./references/ADAPTATION.md).
4. **Run the project's verify commands** — the installation itself must leave
   lint/build/tests green.
5. Commit everything to the default branch (or open a PR if the owner prefers
   to eyeball it — ask once).

## Phase 4 — Smoke test **[TOGETHER]**

Goal: one idea driven through the whole pipeline **manually** before any
schedule exists. Never skip this; never start schedules first.

1. **[OPERATOR or AGENT]** File a small, real test idea (through the board if
   built, else directly as an issue). Vote for it if the board is live.
2. **[AGENT]** Run the Architect exactly as a schedule would: paste its
   activation prompt (in the generated `ai-factory/README.md`) into a fresh
   session. Then verify **every** artifact:
   - [ ] Architect Assessment comment posted, using the template
   - [ ] labels swapped correctly (`stage:submitted` removed; `architect:approved` + one `impact:*` + `coder:queued` added — or the declined pair)
   - [ ] meta block updated, **all other fields preserved**
   - [ ] `CASES.md` entry committed to the default branch
   - [ ] notification email arrived (if enabled)
3. **[AGENT]** Run the Coder the same way, then verify:
   - [ ] branch `factory/idea-<n>-<slug>` cut from the default branch
   - [ ] version bumped in the manifest
   - [ ] verify commands green; PR opened, referencing the issue
   - [ ] merged per the profile's ship definition (W7) — and the deploy actually happened
   - [ ] Implementation Report comment; labels → `coder:executed`; meta + `CASES.md` updated
   - [ ] the shipped change works in the live product **[OPERATOR confirms]**
4. Any failed checkbox: fix the generated files (not the run), then repeat the
   affected run. The instruction files must be good enough that a cold run
   passes — that is the standard.

## Phase 5 — Go autonomous **[OPERATOR]**, guided **[AGENT]**

1. The owner creates the two schedules (runner options and how-to in
   `OPERATOR_GUIDE.md` §6): cadence from the profile (W11), **offset so the
   Coder fires 30–60 minutes after the Architect**, each schedule's prompt
   being the one-liner from the generated `ai-factory/README.md`. Sensible
   range 3–24 h per role; never shorter than a run takes.
2. **Hand the owner the keys** — say this explicitly at the end of setup:
   - the two activation prompts (they re-run a role manually any time);
   - the **pause switch**: disabling a schedule pauses that role; disabling
     both stops the Factory; nothing in the repo needs to change;
   - `RULES.md` is their steering wheel — the one file they edit to steer an
     otherwise unsupervised pipeline (and the roles may never edit it);
   - `WEAKNESSES.md` is the deferred-hardening backlog to revisit as the
     Factory grows.

## Definition of done

- [ ] `FACTORY_PROFILE.md` complete and confirmed by the owner.
- [ ] Bot account + verified token; web layer + email configured if chosen.
- [ ] All pipeline labels exist.
- [ ] `ai-factory/` files generated, project-specific, zero placeholders left,
      each role file passes the cold-read test.
- [ ] One real (tiny) idea shipped end-to-end through manual role runs, every
      checklist item green.
- [ ] Both schedules live and offset; owner has prompts, pause switch, and
      knows `RULES.md` is theirs.
