# 🔁 GAF Adaptation Matrix — swap points

The GAF pattern (blueprint §2's left column) never changes; everything in
this file is a **named swap point** where an installation may substitute its
own implementation. The reference stack — GitHub Issues + Vercel + Resend +
Claude-based runners — is the proven default: prefer it when nothing forces a
substitution, and when substituting, hold the substitute to the same
contract stated in each section.

---

## S1 — Issue tracker (the database)

**Contract:** issues with bodies the roles can rewrite, free-form labels,
comments, an API the serverless layer and the roles can drive, and file
storage for uploads. The tracker *is* the database — pick one, never two.

| Option | Notes |
| ------ | ----- |
| **GitHub Issues** *(reference)* | Labels, contents API for uploads/ledger, one SDK. MCP tooling exists for agent runners. Remember: MCP layers strip hidden HTML comments from bodies — hence the visible meta block, non-negotiable. |
| GitLab Issues | Equivalent feature set (labels, notes, repository files API). Port the API bridge; label grammar unchanged. |
| Gitea / Forgejo | Same shape, self-hosted. Verify the agent runner has API access to it. |
| Jira | Possible (labels or statuses as the state machine) but heavier; issue-body rewriting is clumsier — prefer storing meta in a dedicated custom field instead of the body block. |

**Doesn't satisfy the contract:** chat threads, spreadsheets, form inboxes —
no state machine, no paper trail.

## S2 — Deploy target & ship definition

**Contract:** "shipped" is one written sentence the last pipeline role can
execute and verify. The four standard definitions are in the Workbook (W7);
map them to platforms:

| Platform situation | Ship definition | Pipeline consequence |
| ------------------ | --------------- | -------------------- |
| **Vercel / Netlify / Pages auto-deploy** *(reference)* | merge = ship | Coder self-merges; simplest, proven |
| CI pipeline deploys on merge (Actions, GitLab CI) | merge + confirm the workflow succeeded | Coder waits on the deploy run before close-out |
| Manual / release-train deploys | merge (or PR only); human deploys | close-out (`version-shipped`, ledger "shipped") moves to after the human deploy |
| VPS / servers needing shell commands | a **Deployer role** with ssh/deploy rights runs a written, verifiable, rollback-equipped procedure | see `ROLES.md` §3.3; deploy credentials live only with the Deployer's runner |

## S3 — Email / notification channel

**Contract:** server-to-server notify endpoint guarded by a shared secret;
provider-agnostic events; every message carries one-click unsubscribe; the
address never reaches a browser or a public artifact.

| Option | Notes |
| ------ | ----- |
| **Resend** *(reference)* | Single HTTPS call; domain verification required. |
| Brevo / Mailchimp transactional / SES / Postmark | Same contract; swap the one `fetch` in the notify function. |
| Tracker-native notifications | Free option for tracker-direct intake (W9): watchers get notified by the platform; skip the whole email layer. |
| Internal channels (Slack, CRM-driven email) | For company-internal Factories; notify endpoint posts to the internal system; per-user contact data can then leave the tracker entirely (best for regulated domains, see Workbook W5 🏦). |

## S4 — Runner & models per role

**Contract:** the runner executes an AI agent against the repo on a
schedule, with the bot identity's credentials, firing the one-line
activation prompt. Because instructions live in the repo (R1), **any**
compliant runner produces the same Factory — and each role may use a
different runner or model.

| Option | Notes |
| ------ | ----- |
| **Claude Code Routine / scheduled trigger** *(reference)* | Fresh session per firing, prompt = the one-liner. |
| GitHub Actions cron + agent action | Keeps everything in-repo; the natural place to pin a per-role model (e.g. a stronger model for the Architect's judgment, a faster one for mechanical roles). |
| Claude Code `/loop` | Local recurring loop — good for the supervised first weeks. |
| Any other agent framework | Fine if it can read the repo, drive the tracker API, run the verify commands, and respect the one-idea-per-run stop. |

Scheduling rules travel with every runner: interval ≥ run length, Coder
offset 30–60 min after the Architect, 3–24 h sensible range, disabling a
schedule = pausing the role.

## S5 — Board & serverless platform

**Contract (blueprint §5):** token stays server-side; API endpoints per the
contract; board themed like the host product. The board is a thin skin —
port it to the product's own stack (React/Vue/plain HTML) rather than
imposing the reference's. Vercel functions ↔ Netlify functions ↔ Cloudflare
Workers ↔ a small Express app on the product's existing server are all
equivalent; keep the "one module knows where ideas live" seam
(`ideasApi.js` in the reference) so the backend can change by editing one
file.

## S6 — Domain register: hobby → business → regulated

The same pattern, three postures. This is the most important adaptation —
it's about **authority**, not technology:

| | Hobby / community *(reference)* | Business / product | Regulated / money-adjacent |
| --- | --- | --- | --- |
| Pipeline | Architect → Coder | + Tester recommended | + human gate **required** (Workbook W8) |
| Ship | auto-merge, auto-deploy | merge after Tester pass | Factory prepares; a named human merges/deploys |
| RULES.md Section A | product invariants | + brand, licensing, SLA invariants | + compliance invariants, auto-decline lists (KYC, money movement, disclosures) |
| Contact data | issue meta, private repo | private store | never in the tracker; internal systems only |
| Vote weight | advisory ordering | advisory ordering | advisory only; never justification for scope |
| Weaknesses backlog | revisit when it grows | vote integrity + test gate before launch | all items **plus** audit/logging review before first run |

A human gate does not un-GAF an installation: the Factory still triages,
sketches, builds, verifies, and documents autonomously — the human presses
exactly one button. Losing 5 % of the autonomy buys admissibility in domains
where "no human in the loop" is simply not a legal option.

## S7 — Language & tone

Board copy, email templates, comment templates, and the name generator are
all swappable to the product's language and voice (Workbook W12). The
**structure** of the mandatory comments (Assessment / Implementation Report
headings) is invariant — translate the words, keep the sections, because
downstream roles parse their upstream role's comment by those sections.
