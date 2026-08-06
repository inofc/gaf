---
name: gaf-setup
description: >
  Set up a GAF (Generative AI Factory) in any software project: an autonomous
  pipeline where people propose ideas as issues and scheduled AI roles review,
  build, and ship the winners. Use this skill when a project owner asks to
  install, replicate, or bootstrap a GAF / AI Factory in their repository.
  Requires a human operator working alongside the AI agent — some steps
  (accounts, tokens, secrets, schedules) can only be done by a person.
---

# 🏰 GAF — Generative AI Factory: Setup Skill

**GAF** is the pattern; a **Factory** is one installation of it. This skill
turns any software project — a game, a SaaS product, an iOS app, an internal
tool — into a project with a Factory: ideas flow in as issues, an **AI
Architect** triages them in public, an **AI Coder** builds and ships the
approved ones, and every decision leaves a documented paper trail.

Two live installations inform this skill: **Knight Duel** (browser game —
community web board, voting, email notifications, auto-deploy) and
**Krugozore AI** (private iOS app — solo owner, tracker-direct, no voting,
CI compile gate because the agent cannot build iOS). Every rule below exists
because one of them proved it or broke without it.

**Who executes what.** Every step is tagged:

| Tag | Meaning |
| --- | ------- |
| **[AGENT]** | The AI agent does this alone. |
| **[OPERATOR]** | Only the human can (accounts, tokens, secrets, dashboards, schedules). The agent explains, waits, then verifies. |
| **[TOGETHER]** | A guided conversation: the agent teaches and asks, the human decides, the agent records. |

**Reading order for the agent:** this file end-to-end, then
`references/BLUEPRINT.md`, then `references/ROLES.md`. Open
`references/RUNNERS.md` at Phases 2/4/5, the others when their phase
arrives. Known limitations live in [`WEAKNESSES.md`](./WEAKNESSES.md) —
read once so you can answer owner questions honestly.

---

## How to behave during setup (binds every phase)

These rules exist because their absence cost real hours:

1. **Speak like a human.** The owner may not know what CI, a PR, or "green"
   means — define each term the first time you use it, in one clause. At
   every phase boundary give a three-line status card: *where we are · what
   happens next · what I need from you (or "nothing")*. When you need the
   owner to do something in a UI, give literal click-paths ("open X → click
   Y → paste this"), never abstractions ("configure the runner").
2. **Never let the owner paste a secret into the chat.** Tokens go into the
   runner's environment or the hosting dashboard (see `RUNNERS.md` and the
   operator guide) — the chat transcript is not a secret store. Ask for the
   *name* of where they put it, never the value.
3. **Commit and push at every phase boundary.** Setup sessions get
   interrupted — model switches, disconnects, days between sessions. The
   repo is the only reliable resume state. Copy
   `templates/SETUP_PROGRESS.template.md` to `ai-factory/SETUP_PROGRESS.md`
   at the start, tick it as you go, and push it with each phase.
4. **The profile grows by append.** Decisions made after Phase 1 closes
   become new, dated W-blocks in `FACTORY_PROFILE.md` (W13, W14, …) — never
   silent edits to answered blocks. The profile is the installation's memory.
5. **Verify in the context that will actually run.** The single most
   expensive mistake in a reference install: the setup agent verified *its
   own* GitHub access and declared Phase 2 passed — but a scheduled run is a
   **different session that inherits nothing**, and the first real firing had
   no API access at all. Every access check in this skill means: checked
   *from a test firing of the actual runner*, not from your session.

---

## Phase 0.a — Brief the owner (the wizard card) **[AGENT]**

Before touching anything, tell the owner in plain words what is about to
happen. Adapt this briefing (don't recite it — fit it to their project and
answers you already know):

> **Here's the whole journey, so you know what's coming:**
>
> 1. **I explore your repo** (~10 min, nothing needed from you) — stack,
>    how it builds, how it deploys.
> 2. **We make the decisions together** (~20–40 min of your attention, all
>    multiple-choice with my recommendations) — what ideas are welcome, what
>    must never break, what "shipped" means, how fast the Factory runs.
> 3. **You create the credentials** (~5–15 min, only you can) — I'll give
>    exact click-by-click steps for each one, and I verify each before we
>    continue.
> 4. **I build the Factory** (~20 min, nothing from you) — the rulebook, the
>    two AI role instruction files, the labels, the CI safety net.
> 5. **We test it once by hand** — you file one small real idea; we fire
>    each role manually through the real scheduler and watch it review,
>    build, and ship that idea end to end.
> 6. **You switch on the schedules** — exact clicks from me — and from then
>    on it runs alone. You keep a pause switch and one rulebook file that
>    steers everything.
>
> Two shapes to choose right at the start:
> - **Minimal** — ideas are GitHub issues, no public website, no voting, no
>   emails. Fastest; step 3 shrinks to almost nothing. Right for a private
>   repo or a solo owner. Everything can be upgraded later.
> - **Community** — a public ideas page on your site where people submit and
>   (optionally) vote, with optional email updates. Needs a bot account,
>   hosting, and more of step 3.

Ask which shape fits (recommend Minimal for a private/solo project), record
the answer in the profile, and prune the rest of setup accordingly. Then
create `ai-factory/SETUP_PROGRESS.md` from the template and begin.

## Phase 0 — Orient **[AGENT]**

Goal: know the pattern and the target project before asking the human
anything.

1. Read [`references/BLUEPRINT.md`](./references/BLUEPRINT.md) and
   [`references/ROLES.md`](./references/ROLES.md).
2. Inspect the target repository and record: stack, package manager, the
   **version manifest** (where the version number lives — check whether it
   appears more than once, e.g. an Xcode `project.pbxproj` carries
   `MARKETING_VERSION` per build configuration and all copies must move
   together; match on the setting name, never on line numbers), the real
   verify commands, today's deploy mechanism, default branch, repo
   visibility, and a **file map** (the files the Architect will name in
   sketches — this also seeds the `area:` labels later).
3. **Run the verify commands once** so a later failure is attributable.
   **If you cannot** — toolchain absent, wrong OS (an iOS app on a Linux
   agent), no test suite — do not skip verification; adopt the **CI-gate
   pattern**: instantiate `templates/VERIFY_CI.template.yml` in Phase 3, and
   treat the *first CI run on the unmodified default branch* as the baseline
   check. The check run then **is** the verify command for the Coder.
4. Copy `templates/FACTORY_PROFILE.template.md` to
   `ai-factory/FACTORY_PROFILE.md`, pre-fill everything you determined,
   leave owner decisions empty. Commit and push.

## Phase 1 — Owner's Workbook session **[TOGETHER]**

Goal: every project-specific decision captured in the profile.

1. Open [`references/OWNER_WORKBOOK.md`](./references/OWNER_WORKBOOK.md).
   Present each block as **2–4 concrete options with a recommended default**
   (marked as such), the trade-offs in preview, and batch related blocks —
   W1–W3 together, then W4–W5, then W6–W8, then W9–W12 — rather than one
   long interview. Where the owner is unsure, record the conservative
   default flagged `(default — revisit)`.
2. **After W1–W3, stop and cross-check the answers against each other**
   before continuing. A scope list that excludes something an invariant
   permits (or vice versa) makes the unsupervised Architect's verdicts a
   coin flip. Surface any contradiction and resolve it now — one answer,
   not two.
3. The optional blocks (W13 taxonomy, W14 priority lanes, W15 roadmap /
   placeholder gate) are offered, not imposed — but offer them: all three
   earned their keep in real installs.
4. Read the finished profile back as a summary — especially the invariants,
   the ship definition, and the pipeline gate — and get an explicit
   **"yes, that's my project."** Commit and push.

## Phase 2 — Prerequisites **[OPERATOR]**, verified **[AGENT]**

Goal: only the credentials this installation's profile actually needs, each
verified **from the runner**, not from your session.

1. Determine which steps apply — most Minimal installs need almost none:

   | Profile says | Operator steps needed |
   | ------------ | --------------------- |
   | Minimal, roles run where setup runs | possibly **none** — but see the runner check below |
   | Roles run in scheduled sessions | GitHub access **per schedule** (attach the repo to each Routine, or a token in the runner env — `references/RUNNERS.md`) |
   | Web board (Community shape) | bot account + token (operator guide O1–O3), hosting + env vars (O4) |
   | Email notifications | provider account + verified sender (O5) |

2. Token type, when one is needed: see the **decision table in
   `OPERATOR_GUIDE.md` O3** — fine-grained single-repo when the owner owns
   the repo (smallest blast radius); classic `repo`-scope **only** for the
   bot-as-collaborator case, where fine-grained tokens 404.
3. **Verify from the runner [AGENT]:** create the role schedules in
   *disabled / manual-only* form (or a throwaway test schedule), **fire one
   test run**, and confirm inside it that the role can list the
   repository's issues. Your own session's access proves nothing about a
   scheduled session's. Do not proceed to Phase 3 on a promise — only on a
   passed check *from the runner*.

## Phase 3 — Build **[AGENT]**

Templates + profile = installation. If a needed value is missing, go back
to the owner rather than inventing one.

1. **Labels.** Generate the full set into `ai-factory/labels.json` from
   `templates/labels.json`: the pipeline base set, minus anything the
   profile disables (no notifications → no `unsubscribed`), plus the
   taxonomy the profile chose — `type:*` as given, **`area:*` generated
   from the Phase 0 file map**, `flag:*`, and the priority label if W14 is
   on. Create them via `templates/FACTORY_LABELS.template.yml` (a
   sync-on-push workflow using the platform's built-in CI token — works
   even where the agent has no label API, keeps labels in sync forever,
   and its `renames:` block renames in place so a renamed label keeps its
   identity on existing issues). Beware label/product name collisions: if
   the product has or will have a user-facing area called X, don't also use
   `area:X` to mean something else.
2. **Instantiate `ai-factory/`** from `templates/`: `RULES.md` (numbering
   is fixed — A1–A7 as templated, custom invariants start at **A8**, and
   roles cite these numbers so never renumber later), `ARCHITECT.md`,
   `CODER.md`, `CASES.md`, `README.md`, plus `ROADMAP.md` if W15 is on.
   Replace **every** placeholder; delete every template-note; each file
   must pass the **cold-read test** — an agent with zero context beyond
   that one file (and no assumption of any particular tooling being
   available) can execute the role.
3. **CI gate** (if Phase 0 chose it): instantiate
   `templates/VERIFY_CI.template.yml`, plus whatever the build needs that
   the repo lacks (e.g. Xcode: a **shared scheme** — per-user schemes in
   `xcuserdata/` are invisible to CI).
4. **(Community shape)** implement the board + serverless API per
   `BLUEPRINT.md` §5 in the product's own stack and voice.
5. Run the verify commands (or trigger the baseline CI run on the default
   branch). Commit everything — to the default branch, or via PR if the
   owner prefers to eyeball it (ask once). Note: some managed agent
   environments cannot delete remote branches or push certain refs — leave
   stale branches and tell the owner to delete them in the UI.

## Phase 4 — Smoke test **[TOGETHER]**

One idea driven through the whole pipeline before any schedule exists —
**and driven through the production runner, not through your own session.**
Pasting the activation prompt into your session would use *your* access and
mask exactly the runner failures this test exists to catch. Fire the real
Routine/schedule manually, once per role.

1. **[OPERATOR or AGENT]** File a small, real test idea — `impact:minor`,
   one file, so the first run tests the *pipeline*, not the code.
2. **[AGENT]** Fire the Architect's runner; verify every artifact:
   - [ ] run had API access (a "queue empty" on a non-empty queue = an
         access failure mis-reported — the role files define **BLOCKED**
         for this; treat it as Phase 2 failed)
   - [ ] Assessment comment posted, on template, taxonomy line included
   - [ ] labels swapped correctly; classification labels applied
   - [ ] meta block added/updated, all other fields preserved
   - [ ] `CASES.md` entry committed to the default branch
3. **[AGENT]** Fire the Coder's runner; verify:
   - [ ] branch `factory/idea-<n>-<slug>` cut from the default branch
   - [ ] version bumped in **every** copy in the manifest
   - [ ] verify green — as a real check run if CI-gated; a run that stays
         `queued` forever or never appears is a **platform/billing
         problem, not a code problem** (operator guide, troubleshooting)
   - [ ] PR opened referencing the issue; merged per the ship definition
         (or correctly held, if gated)
   - [ ] Implementation Report posted; labels → `coder:executed`; meta +
         `CASES.md` updated
   - [ ] the shipped change works in the live product **[OPERATOR
         confirms]** — on-device / in-browser, per the profile's manual
         verification
4. Any failed checkbox: fix the generated files (not the run), re-fire.
   The standard is that a cold run passes.

## Phase 5 — Go autonomous **[OPERATOR]**, guided **[AGENT]**

1. Walk the owner through creating/enabling the two schedules using the
   **exact click-paths in [`references/RUNNERS.md`](./references/RUNNERS.md)**
   for their runner — including that runner's trap list (for Claude
   Routines: *the repository must be attached to each Routine* or the role
   runs blind). Cadence from the profile; Coder offset 30–60 min after the
   Architect; interval ≥ run length. Remind: every firing is a billed agent
   session even when the queue is empty — hourly cadences mean ~720
   mostly-empty sessions a month; 6–24 h is the sensible steady state.
2. Per-role models (optional): the Coder writes code blind — give it the
   strongest model available; the Architect can run a tier down. Set in the
   runner, not in the repo.
3. **Hand the owner the keys**, explicitly: the two activation prompts (in
   the generated `ai-factory/README.md`); the **pause switch** (disable a
   schedule = pause a role; nothing in the repo changes); **`RULES.md` is
   their steering wheel** (only they edit it); `WEAKNESSES.md` is the
   deferred-hardening backlog. Mark `SETUP_PROGRESS.md` complete and push.

## Definition of done

- [ ] Owner was briefed with the wizard card and chose a setup shape.
- [ ] `FACTORY_PROFILE.md` complete, cross-checked, owner-confirmed.
- [ ] Credentials verified **from a test firing of the actual runner**.
- [ ] All labels exist (pipeline + taxonomy), created by the sync workflow.
- [ ] `ai-factory/` files generated, zero placeholders, cold-read clean.
- [ ] One real idea shipped end-to-end **through the production runner**,
      every checklist item green, owner confirmed it live.
- [ ] Schedules on and offset; owner has prompts, pause switch, rulebook.
- [ ] `SETUP_PROGRESS.md` fully ticked and pushed.
