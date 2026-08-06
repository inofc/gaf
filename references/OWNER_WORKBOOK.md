# 📖 GAF Owner's Workbook — the guided interview

This workbook exists because a Factory is only as good as the decisions its
owner feeds it. Each block below is one decision. The agent running the setup
walks the owner through the blocks **in order**, and for each one: presents
the explanation and examples first, then asks, then records the answer in
`ai-factory/FACTORY_PROFILE.md`. The filled profile is the installation's
configuration — every template placeholder is filled from it.

**To the owner:** there are no wrong stacks here, only unstated assumptions.
The Factory will run unsupervised; anything you don't say out loud in this
session, it cannot know. Where you're unsure, take the conservative default —
every block names one — and revisit later (the profile is yours to edit;
regenerating the role files from it is a small agent task).

**The four example domains** used throughout:
🎮 **Game** — real answers from Knight Duel, the reference installation.
📦 **SaaS / digital product** — a small invoicing web app.
🌐 **Website builder** — a template-based site-builder product.
🏦 **Regulated / banking** — an online savings-account product. (Shown to
mark where the pattern must bend: regulated domains need a human gate.)

---

## W1 — What is the product, and what do users propose ideas about?

**📖 What & why.** One paragraph the Factory will see in every role file: what
the product is, who uses it, and what kind of ideas are in scope. The
Architect judges "fit" against this — a vague answer produces vague verdicts.

**🧭 How to think.** Complete the sentence: *"Users of ___ propose ideas
about ___, and should not propose ideas about ___."* The exclusion half
matters as much as the inclusion half.

**💡 Examples.**
- 🎮 "A timing-based browser fighting game. Players propose gameplay tweaks,
  visual/audio polish, and UI improvements — not new game modes requiring
  servers, and not anything about the ideas pipeline itself."
- 📦 "An invoicing app for freelancers. Users propose workflow improvements,
  template options, export formats — not payment processing changes or
  pricing-plan changes."
- 🌐 "A site builder. Users propose new block types, theme options, editor
  ergonomics — not hosting-infrastructure or customer-billing changes."
- 🏦 "A savings product. Customers propose UX and clarity improvements to the
  app — never product terms, rates, eligibility rules, or anything touching
  money movement (those are excluded by rule, see W2)."

**✍️ Record in profile:** `product_description`, `ideas_in_scope`,
`ideas_out_of_scope`.

## W2 — Protected invariants (→ RULES.md Section A)

**📖 What & why.** The things your product would die without. The Factory will
refuse **any** idea that breaks these, no matter how many votes it has —
this list is the single strongest control you have over an unsupervised
pipeline. Four invariants are mandatory in every installation (core value
sacred; end-to-end usability on all supported platforms; fixed tech scope;
the Factory may not modify itself + no security-sensitive changes). This
block captures what's **specific to you**.

**🧭 How to think.** Imagine the worst idea that could reach the top of the
vote board — the one that would be popular *and* ruinous. What must survive
it? Name your core loop / core value explicitly (what makes the product the
product), your supported platforms, and anything with legal or contractual
weight. If losing it would make you roll back the release by hand, it's an
invariant.

**💡 Examples.**
- 🎮 "Block/parry/attack timing, the stamina economy, and escalating rounds
  *are* the game — decline invincibility, infinite stamina, auto-win.
  Playable on desktop + mobile after every ship (touch, keyboard, pause, iOS
  audio unlock, canvas performance). Family-friendly cartoon violence only;
  no monetization."
- 📦 "Invoices, once issued, are immutable — no idea may allow editing an
  issued invoice. Tax calculations follow the configured jurisdiction table
  and are never 'simplified'. Export to PDF must keep working."
- 🌐 "Published customer sites must never break from an editor change —
  editor and renderer are separate; ideas touch the editor only. Every
  template stays responsive and accessibility-clean (WCAG AA)."
- 🏦 "Nothing that moves, displays, or calculates money may change without
  human sign-off (see W8 — this domain replaces auto-ship with a human
  gate). Regulatory disclosures are immutable. No idea may touch KYC,
  authentication, or transaction flows — auto-decline."

**✍️ Record in profile:** `invariants` (numbered A-rules, ready for the
RULES template).

## W3 — Fixed tech scope

**📖 What & why.** The boundary of what the Coder may build with. Without it,
a well-meaning idea ("add user accounts!") quietly drags in a database, a
login system, and a monthly bill. The Factory declines anything outside this
scope instead of asking you mid-run — there is no mid-run.

**🧭 How to think.** List the current stack and mark it as *the* stack. Then
decide the dependency policy: the reference default is "no new backends,
paid services, accounts/logins, or heavyweight dependencies; small
well-known packages allowed for `impact:moderate`+ ideas if justified in the
assessment." Tighten or loosen deliberately.

**💡 Examples.**
- 🎮 "React + Vite + HTML canvas, synthesized audio, existing serverless API
  only. No DB, no logins, no paid services."
- 📦 "Rails + Postgres + Hotwire as-is. New gems need to be >10k downloads/
  week and MIT/Apache licensed. No new external services without the owner."
- 🏦 "Approved internal platform components only; any new dependency
  whatsoever is out of scope for the Factory — decline and refer to the
  platform team."

**✍️ Record in profile:** `tech_scope`, `dependency_policy`.

## W4 — Content & conduct policy

**📖 What & why.** What submitted ideas (and shipped content) may not
contain. The Architect declines violating ideas; the decline reason must not
repeat the offending content.

**🧭 How to think.** Start from the reference set — no hateful/sexual/graphic
content, no gambling mechanics or dark patterns, no copyrighted or
trademarked characters/assets/music, no depictions of real people — then add
your domain's specifics (brand voice, age rating, jurisdiction rules).

**💡 Examples.** 🎮 Reference set + "family-friendly cartoon violence only".
📦 Reference set + "no ideas that generate legal/tax advice wording".
🏦 Reference set + "no marketing claims about returns; regulated wording
comes only from compliance".

**✍️ Record in profile:** `content_policy`.

## W5 — Privacy posture

**📖 What & why.** What personal data the Factory touches and where it lives.
The pattern's baseline: submitter emails (if collected) are server-side
only — never in comments, commits, the ledger, code, or the board; no new
personal-data collection ever, by rule; ideas containing third-party
personal data are declined without repeating the data.

**🧭 How to think.** Two decisions: **(1)** Collect submitter emails at all?
(Only needed for status notifications — W10.) **(2)** Where do they live?
In the issue meta block is acceptable **only while the repo is private**; a
public repo requires a separate private store keyed by issue number. If your
domain has data-protection obligations (GDPR, banking secrecy), note the
retention/deletion answer here too.

**💡 Examples.** 🎮 "Optional email in issue meta; repo stays private;
one-click HMAC unsubscribe erases it." 🏦 "No emails in the tracker at all —
notification identity lives in the bank's existing CRM; the Factory calls an
internal notify endpoint with the issue number only."

**✍️ Record in profile:** `collect_emails` (yes/no), `email_store`
(issue-meta-private-repo | private-store | none), `privacy_notes`.

## W6 — Verify commands

**📖 What & why.** The exact commands that must pass before anything ships.
The Coder runs these and refuses to ship on failure — they are the entire
automated quality gate (until you add a Tester role), so name everything
that exists.

**🧭 How to think.** List the real commands (`npm run lint`, `npm run build`,
`pytest`, `make test`, …) — the agent verified in Phase 0 that they pass
today. If the honest answer is "there are no tests", say so and note it: the
Factory still works (lint + build gate), but log "add a test gate" in your
copy of the weaknesses backlog. Also state what *manual* verification means
for your product ("play a round", "issue a test invoice", "publish a test
site").

**💡 Examples.** 🎮 `npm run lint` + `npm run build`, manual play-test via
`npm run dev` when gameplay changes. 📦 `bundle exec rubocop` + `rails test`
+ `rails test:system`. 🏦 full CI suite green **and** the human gate (W8) —
commands alone never ship here.

**✍️ Record in profile:** `verify_commands`, `manual_verification`.

## W7 — Definition of "shipped"

**📖 What & why.** The single sentence the Coder executes as its final step.
Ambiguity here is how half-deployed features happen — the Factory needs one
written definition, not a vibe.

**🧭 How to think.** Pick one:
1. **Auto-deploy on merge** *(reference standard, recommended when
   available)* — the default branch auto-deploys (Vercel/Netlify/Pages);
   merging **is** shipping; the Coder merges its own PR once checks are
   green.
2. **CI deploys on merge** — same as 1, but the Coder must also confirm the
   deploy workflow succeeded before closing out.
3. **Merge, human deploys** — the Coder merges (or only opens the PR) and the
   idea is "shipped" only when a human deploys; the close-out moves
   accordingly.
4. **A Deployer role deploys** — merging sets `deployer:queued`; see
   `ROLES.md` §3.3.

**💡 Examples.** 🎮 Option 1 — Vercel deploys `main`; six ideas shipped this
way. 📦 Option 2 — GitHub Actions deploys on merge; Coder waits for the
workflow. 🏦 Option 3/4 with a mandatory human approval on the PR — in this
domain the Coder **never** self-merges (see W8).

**✍️ Record in profile:** `ship_definition` (one of the four, plus the
concrete platform details).

## W8 — Pipeline shape & the human gate

**📖 What & why.** Which roles run, in what order, and whether a human
approval sits anywhere in the chain. Default: **Architect → Coder**, fully
autonomous — that is the proven reference shape. Extensions (Tester,
Deployer) and a human gate are supported; recipes in `ROLES.md` §3.

**🧭 How to think.** Start with the default unless you have a reason. Reasons
to deviate: no auto-deploy exists (→ Deployer), behavioral quality matters
more than shipping speed (→ Tester), or **your domain is regulated /
customer-money-adjacent** — then a human approval gate is not a weakness,
it's a requirement: keep the Factory's triage/build/report machinery but
make the last mechanical step (merge or deploy) require a named human.
Decide also who that human is and the maximum time an idea may wait on them.

**💡 Examples.** 🎮 Architect → Coder, no gate. 📦 Architect → Coder →
Tester, no gate. 🏦 Architect → Coder (PR only) → **human reviews & merges**
→ Deployer; the Factory does everything except press the button.

**✍️ Record in profile:** `pipeline` (ordered role list), `human_gate`
(none | position + who + max-wait).

## W9 — Ideas intake: web board or tracker-direct?

**📖 What & why.** The Factory works identically either way; the web board
only lowers the barrier for non-technical users — at the cost of building
and hosting the board page + serverless API (Blueprint §5).

**🧭 How to think.** Public/consumer audience → board (they will not file
GitHub issues). Developer or internal audience → tracker-direct is free and
already done. You can start tracker-direct and add the board later without
touching the pipeline.

**✍️ Record in profile:** `intake` (board | tracker-direct), and if board:
where it's hosted and the product's visual theme to match.

## W10 — Notifications

**📖 What & why.** Optional emails to submitters at each stage (submitted →
verdict → building → shipped). They close the loop that makes people submit
again — but they require an email provider account, a verified sender, and
the privacy posture from W5.

**🧭 How to think.** Consumer-facing with a board → worth it. Internal or
tracker-direct → skip; watchers on the issue already get notified by the
tracker. If yes: transactional provider (reference: Resend; alternatives in
`ADAPTATION.md`), always with one-click unsubscribe.

**✍️ Record in profile:** `notifications` (yes/no), `email_provider`,
`sender_address`.

## W11 — Cadence

**📖 What & why.** How often each role wakes. This is also your throughput
and your cost lever — each Coder run is a full agent build session.

**🧭 How to think.** Rules: never schedule below the length of a run
(Architect: minutes; Coder: 15–60+ min); offset the Coder 30–60 min after
the Architect so fresh approvals build in the same cycle; 3–24 h per role is
the sensible range. Start slow (daily), speed up when the queue outgrows the
cadence. The pause switch is always there.

**💡 Examples.** 🎮 Architect every 3 h, Coder at +30 min. 📦 Both daily,
09:00/10:00. 🏦 Weekly, aligned to the release train and the human gate's
availability.

**✍️ Record in profile:** `architect_schedule`, `coder_schedule` (+ one per
extension role), stated in the owner's timezone with the UTC conversion.

## W12 — The Factory's voice & identity

**📖 What & why.** The Factory talks to your community — board copy, email
wording, the random display-name generator, the bot account's name. A voice
consistent with the product makes the Factory feel like a feature, not a
bolt-on.

**🧭 How to think.** Name the bot (visible on every comment and commit),
pick the tone, and theme the name generator to your product's world.

**💡 Examples.** 🎮 Bot "AI_Factory_Infca"; knight-themed names ("Sir
Lancelot of the Parry"); playful medieval tone. 📦 "invoicebot"; neutral
professional tone; name generator off. 🏦 Bank-approved wording only; no
name generator; formal tone reviewed by compliance once.

**✍️ Record in profile:** `bot_name`, `tone`, `name_generator`
(on/off + theme).

---

## Closing the session

The agent reads the completed profile back as a summary — especially W2
(invariants), W7 (ship definition), and W8 (pipeline & gate) — and asks the
owner to confirm: **"Is this your project?"** Only a yes moves setup to
Phase 2. Fields answered by default get the `(default — revisit)` flag so
they surface in the handover at Phase 5.
