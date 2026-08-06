# 📖 GAF Owner's Workbook — the guided interview

This workbook exists because a Factory is only as good as the decisions its
owner feeds it. Each block below is one decision. The filled profile is the
installation's configuration — every template placeholder is filled from it.

**To the agent — how to run this session (learned from real installs):**

- **Multiple choice beats interview.** For each block, present **2–4
  concrete options with a recommended default marked "(Recommended)"** and
  the trade-offs visible — not an open question. Owners answer option sets
  in seconds; open questions stall sessions.
- **Batch the blocks**: W1–W3, then W4–W5, then W6–W8, then W9–W12, then
  offer the optional W13–W15. Do not skip the one-paragraph teaching part
  before each block.
- **After W1–W3, cross-check the answers against each other** before going
  on. If the scope (W1) excludes something an invariant (W2) permits — or
  permits something an invariant forbids — the unsupervised Architect's
  verdict on that topic becomes a coin flip. Resolve to one answer now. The
  clean pattern for "not now, maybe later" topics is an **owner-gated
  rule**: the Architect assesses such ideas publicly, then always declines
  "requires owner decision (rule N)" — only the owner promotes one by hand.
- **Rule numbering is fixed.** The RULES template reserves **A1–A7**
  (core value, usability, tech scope, factory-may-not-modify-itself,
  security, content, monetization). Owner-specific invariants start at
  **A8**. Record numbers accordingly in the profile — roles cite rule
  numbers, and renumbering after install breaks every citation.
- Where the owner is unsure, record the conservative default flagged
  `(default — revisit)`.

**To the owner:** there are no wrong stacks, only unstated assumptions. The
Factory runs unsupervised; anything you don't say here, it cannot know.

**The four example domains:** 🎮 game (Knight Duel, reference), 📱 solo app
(Krugozore AI, a private iOS app — the "Minimal" reference), 📦 SaaS,
🏦 regulated/banking (where the pattern must bend: human gate required).

---

## W1 — What is the product, and what do users propose ideas about?

**📖 What & why.** One paragraph every role file carries: what the product
is, who uses it, what ideas are in scope. The Architect judges "fit"
against this — a vague answer produces vague verdicts.

**🧭 How to think.** Complete: *"Users of ___ propose ideas about ___, and
should not propose ideas about ___."* The exclusion half matters as much.

**💡 Examples.** 🎮 "Gameplay tweaks, polish, UI — not new modes needing
servers, not the pipeline itself." 📱 "Chat UX, catalogue features,
onboarding polish, accessibility — nothing needing a server, an account, or
a second AI provider." 🏦 "UX and clarity only — never terms, rates, or
money movement (excluded by rule, W2)."

**✍️ Record:** `product_description`, `ideas_in_scope`, `ideas_out_of_scope`.

## W2 — Protected invariants (→ RULES.md Section A, from A8 up)

**📖 What & why.** What your product would die without. The Factory refuses
**any** idea that breaks these, whatever its support — this is your
strongest control over an unsupervised pipeline. A1–A7 are templated;
this block captures what's specific to you, numbered from A8.

**🧭 How to think.** Imagine the worst idea that could become popular —
popular *and* ruinous. What must survive it? Name the core loop/value,
supported platforms, anything with legal weight. If losing it would make
you roll back a release by hand, it's an invariant. Topics you want to
keep *discussable* but never auto-built (cloud sync in a privacy app, a
feature you haven't designed yet) become **owner-gated** rules, not flat
bans.

**💡 Examples.** 🎮 "Block/parry timing and stamina *are* the game —
decline invincibility. Playable desktop + mobile after every ship."
📱 "Privacy is the product: no accounts, no telemetry, chats stay
on-device; the free-model training warning may never be weakened. Cloud
sync = owner-gated." 🏦 "Nothing that moves or displays money changes
without human sign-off; KYC/auth flows auto-declined."

**✍️ Record:** `invariants` (numbered from A8, plus any rewording of A1–A7).

## W3 — Fixed tech scope

**📖 What & why.** The boundary the Coder builds within. Without it, a
well-meaning idea quietly drags in a database, a login system, a bill.

**🧭 How to think.** List the current stack as *the* stack; then a
dependency policy. Reference default: "no new backends, paid services,
logins, or heavyweight dependencies; small well-known packages allowed for
`impact:moderate`+ if justified." A stricter stance that has proven itself:
**zero third-party dependencies, permanently** — auditable-by-reading is
itself a product feature for privacy-sensitive apps.

**✍️ Record:** `tech_scope`, `dependency_policy`.

## W4 — Content & conduct policy

Reference set: no hateful/sexual/graphic content, no gambling mechanics or
dark patterns, no copyrighted/trademarked assets, no depictions of real
people. Add your domain's specifics. Declines must not repeat the offending
content. **✍️ Record:** `content_policy`.

## W5 — Privacy posture

**📖 What & why.** What personal data the Factory touches, if any.

**🧭 How to think.** The strongest position, proven in the Minimal install:
**collect nothing** — no email field, no notification pipeline; the
tracker's own notifications cover participants; the repo could go public
tomorrow with nothing to scrub (kills weakness K5 entirely). If you do
collect emails (Community shape + notifications): issue-meta storage is
acceptable **only while the repo is private**; a public repo requires a
separate private store.

**✍️ Record:** `collect_emails`, `email_store`
(`none | issue-meta-private-repo | private-store`), `privacy_notes`.

## W6 — Verification

**📖 What & why.** What must pass before anything ships — the entire
automated quality gate.

**🧭 How to think.** List the real commands the agent verified in Phase 0.
**If the agent cannot run the toolchain at all** (iOS on a Linux agent, no
local build possible), the answer is the **CI-gate pattern**: a workflow
(from `templates/VERIFY_CI.template.yml`) builds on every push; "verify
commands pass" means "the check run concluded success"; the Coder polls it
and may resume across activations. Also record what *manual* verification
means (a human, on a device) — no role can do it, and it is why W8 gates
big changes. If there are no tests, say so and log "add a test gate" in
the weaknesses backlog.

**✍️ Record:** `verify_commands` (or the CI-gate description),
`manual_verification`.

## W7 — Definition of "shipped"

**📖 What & why.** The single sentence the last role executes. Pick one:
1. **Auto-deploy on merge** *(reference)* — merging is shipping.
2. **CI deploys on merge** — Coder also confirms the deploy run.
3. **Merge is the finish line; a human releases** *(proven for app-store
   products)* — the Factory ships *code to `main`*; cutting the
   TestFlight/store build stays the owner's job, out of band; `CASES.md`
   becomes the release-notes source. Never let a role claim "live for
   users" under this definition.
4. **A Deployer role deploys** — see `ROLES.md` §3.3.

**✍️ Record:** `ship_definition` + platform details.

## W8 — Pipeline shape & the human gate

**📖 What & why.** Which roles run and whether a human sits anywhere.
Default: Architect → Coder, autonomous. A **partial gate** is often the
sweet spot: `impact:minor/moderate` self-merge; **`impact:major` stops at
the PR for the owner** (with a max-wait, e.g. 7 days, then the Coder posts
one reminder and moves on — never blocks the queue). Regulated domains:
full human gate, required not optional.

Note honestly: when the roles run under the owner's own account (W16 in
some installs), the gate is **convention, not enforcement** — branch
protection cannot bind the repo owner. Record that consciously.

**✍️ Record:** `pipeline`, `human_gate` (none | partial: which impact +
who + max-wait | full).

## W9 — Intake: how do ideas arrive? *(orthogonal to voting — see W9b)*

**📖 What & why.** Two proven modes; the Factory pipeline is identical in
both, and you can start with the first and add the second later without
touching the roles.

- **Tracker-direct** *(default; right for private repos, solo owners,
  developer audiences)* — ideas are issues, filed by whoever has access.
  Zero infrastructure. Ideas filed this way have no meta block; the
  Architect adds one on first review.
- **Web board** *(the Knight Duel shape; right for non-technical
  audiences)* — a page on the product's site + serverless functions
  holding a **server-side token** (the token must never reach the
  browser; hosting env vars only — operator guide O3/O4). Blueprint §5 has
  the full contract.

**✍️ Record:** `intake` (`tracker-direct | board`), board host/theme if
board.

## W9b — Voting: is community priority a thing here?

**📖 What & why.** **Voting is optional, not the base scenario.** The base
scenario is: ideas arrive, the Architect triages them in order. Voting is
an upgrade for real multi-user communities — a solo or small-team install
gets nothing from it but dormant machinery.

**🧭 Options.**
1. **No voting** *(default)* — pick order is: priority lane first (W14, if
   on), then oldest-first. Simple, honest for solo installs.
2. **Reactions voting** — 👍 reactions on the issue are the vote count.
   Zero infrastructure, works on a private repo today and keeps working if
   it goes public. Good middle ground.
3. **Board voting** *(requires the board, W9)* — a vote button writing to
   the meta block via the API. Most visible; also the least protected (see
   weakness K1 — advisory ordering only, never justification for scope).

Whatever you choose, the meta block keeps its `votes` field so a later
upgrade needs no migration; the roles' **pick order is generated from this
answer** into both role files.

**✍️ Record:** `voting` (`none | reactions | board`), and the resulting
pick-order sentence.

## W10 — Notifications

Optional emails to submitters at each stage. Only meaningful with a board
and a public audience; tracker-direct participants are already notified by
the platform. **No is the default** — it also deletes an entire operator
step (O5) and a class of privacy obligations (W5). If yes: transactional
provider, verified sender, one-click unsubscribe, always.

**✍️ Record:** `notifications` (yes/no), `email_provider`, `sender_address`.

## W11 — Cadence

**📖 What & why.** How often each role wakes — and your **cost lever**.
Two costs, name both: CI minutes (only when builds actually run) and
**agent sessions** — every firing is a billed session *even when the queue
is empty*. An hourly Architect ≈ 720 sessions/month, most reporting "queue
empty."

**🧭 How to think.** Interval ≥ run length (Architect: minutes; Coder:
15–60+); Coder offset 30–60 min after the Architect; **6–24 h per role is
the sensible steady state**. A faster bring-up cadence for the first days
is fine — flag it `(bring-up — revisit)` so it resurfaces at handover.

**✍️ Record:** `architect_schedule`, `coder_schedule` (cron, UTC).

## W12 — Voice & identity

Bot name (or role-attribution convention if running under the owner's
account — e.g. commits authored "Factory (Coder)" with a role banner on
every comment, so the paper trail stays readable under one identity), tone
for the public comments, name generator on/off. The voice must never
obscure the substance: verdict, rating, sketch, risks stay skimmable.

**✍️ Record:** `bot_name` / attribution convention, `tone`,
`name_generator`.

---

# Optional blocks — offer each; all three earned their keep

## W13 — Issue taxonomy (labels beyond the pipeline)

**📖 What & why.** The pipeline labels track *state*; a taxonomy makes the
tracker *navigable*. Three dimensions, applied by the Architect to every
idea (approved and declined alike), stated on the first line of each
Assessment:

- **`type:*` — exactly one per idea** (mutual exclusivity is what makes
  filtering meaningful): `feature / bug / polish / accessibility /
  performance / infra / docs / refactor`. Scope `type:infra` to the
  product's build configuration **explicitly excluding** the Factory's own
  files — otherwise it becomes a smuggling route past A4.
- **`area:*` — one or more**, generated by the agent **from the Phase 0
  file map** (the Architect already names files in its sketch; the labels
  fall out free). Add a catch-all area for project config/docs. **Avoid
  collisions with product vocabulary**: if the product has a user-facing
  section called "Projects", don't name the build-config area
  `area:project`.
- **`flag:*` — cross-cutting and behaviour-changing**, not decorative:
  e.g. `flag:privacy` = the Architect must state how the privacy invariant
  survives, and the Coder must devote a report line to it; a
  `flag:placeholder` pairs with W15's gate.

**✍️ Record:** the chosen type list, the area map (label → file/surface),
each flag and the behaviour it triggers.

## W14 — Priority lanes

**📖 What & why.** Some submitters' ideas should be picked first —
typically the owner's. Two rules keep it safe: **identify by the platform
author** (the issue's `user.login`), *never* by any display-name field in
the meta block (submitter-controlled text — anyone could type the owner's
name); and **priority orders the queue, it never exempts from the rules**
— the owner's route around a rule is editing RULES.md, not filing an
issue. Add a visible label (e.g. `priority:owner`) so the ordering is
auditable. On a solo private repo this lane is dormant but costs nothing
and activates the day the repo opens up.

**✍️ Record:** `priority_lane` (off | GitHub usernames), label name.

## W15 — Roadmap & the placeholder gate

**📖 What & why.** If the product has **deliberately unbuilt surfaces** —
placeholder sections, "coming soon" teasers — they are an attractive
nuisance for an autonomous Coder: a plausible implementation compiles,
passes CI, self-merges, and lands *someone else's guess at your design* in
`main`. Two artifacts fix this:

- **`ai-factory/ROADMAP.md`** — where the product is going, marked
  explicitly *"intent, not a specification — no role may build from this
  file."* It also pre-empts false "duplicate" reports when a feature is
  deliberately visible in two places.
- **An owner-gated invariant** (same shape as any owner-gated rule):
  *polish* of placeholder surfaces (copy, layout, accessibility, adding a
  teaser) is normal work; *implementing* the feature behind one is
  assessed publicly, then declined "unbuilt section — requires owner
  design (rule N)". When you're ready to build one, you specify it and
  lift the gate by editing RULES.md.

**✍️ Record:** the placeholder surfaces list, the rule number, whether to
generate ROADMAP.md.

---

## Closing the session

Read the completed profile back — especially W2 (invariants), W7 (ship),
W8 (gate), and the W9/W9b intake-voting pair — and ask: **"Is this your
project?"** Only a yes moves to Phase 2. Decisions made after this point
enter the profile as new, dated W-blocks — never silent edits.
