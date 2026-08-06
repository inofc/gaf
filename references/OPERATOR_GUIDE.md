# 🧑‍🔧 GAF Operator Guide — the human-only steps

Some parts of a Factory can only be done by a person: accounts, tokens,
secrets, schedules. This guide walks the operator (usually the owner)
through each one, with the agent verifying every step before setup
continues. Written for GitHub + Vercel + Resend; substitutions in
[`ADAPTATION.md`](./ADAPTATION.md); runner-specific click-paths in
[`RUNNERS.md`](./RUNNERS.md). Every trap below was hit for real.

**Which steps apply to *you*** — check the profile first; a Minimal
install skips most of this:

| Profile choice | Steps needed |
| -------------- | ------------ |
| Tracker-direct, no board, no email, owner runs the roles | **O6 only** (schedules + per-schedule repo access) — O1–O5 all skipped |
| Web board (Community) | O1–O4, O6 |
| Email notifications | + O5 |

**Secrets hygiene, always:** never paste a token into the chat with the
agent — a transcript is not a secret store. Tokens go into the runner's
environment or the hosting dashboard; tell the agent the *name* of the
variable, never the value.

---

## O1 — Create the Factory bot account *(board installs; optional otherwise)*

A **dedicated machine account** gives the Factory its own identity and its
token a tiny blast radius. It is **required** for the web-board shape (the
board's server needs credentials that are not yours) and recommended once
untrusted users can file content the roles will read.

A **solo/private install may skip the bot** and run under the owner's
account — with three consequences to accept knowingly (record them in the
profile): any human gate is convention, not enforcement (branch protection
cannot bind the repo owner); the platform won't notify you about your own
account's actions, so the issue list and `CASES.md` become pull-not-push;
and the paper trail needs the role-attribution convention (W12) to stay
readable. Revisit the bot decision before the repo goes public.

If creating one: fresh email (+alias works), **enable 2FA**, keep the
email private (noreply commits), name per W12. One machine account per
person is within GitHub's free-tier rules.

## O2 — Grant Write access (not admin) *(bot installs only)*

Repo → Settings → Collaborators → add the bot with **Write**. The bot
**must accept the invite** — a pending invite behaves exactly like no
access (classic source of mysterious 404s).

> ⚠️ **Branch protection:** if the default branch requires PR reviews, a
> lone Write collaborator cannot self-merge — the "Coder merges itself"
> ship definition breaks. Exempt the bot, drop the requirement, or choose
> the human-gate ship definition — decide it, don't discover it.

## O3 — Generate the token: pick the right *kind*

The right token type depends on **who owns the repo relative to the
account holding the token** — getting this wrong produces confusing 404s:

| Situation | Token type | Why |
| --------- | ---------- | --- |
| Owner's own account, owner-owned repo *(solo installs)* | **Fine-grained PAT, scoped to that one repo** | Smallest blast radius: Contents + Issues + Pull requests R/W, Actions Read, Metadata Read. An injected idea (weakness K3) reaches one repo, not every repo you own. |
| Bot as collaborator on a repo owned by a *different personal account* *(reference board setup)* | **Classic PAT, `repo` scope** | Fine-grained PATs cannot reach a private repo owned by another personal account even with an accepted invite — the API answers 404. Confirmed the hard way. |
| Bot in an org that owns the repo | Fine-grained works | Resource owner = the org. |

Long expiry or a rotation reminder. **Verify (agent, the moment it
lands — from wherever the token will actually be used):**

```bash
curl -s -H "Authorization: Bearer <TOKEN>" https://api.github.com/repos/<owner>/<repo>/issues?state=open | head -5
# expect a JSON array — not "Not Found" / "Bad credentials" / 403
```

## O4 — Hosting & environment variables *(web board only)*

Import the repo into the hosting platform; add in Project → Settings →
Environment Variables:

| Name | Value | Required for |
| ---- | ----- | ------------ |
| `GITHUB_TOKEN` | the token from O3 | submit + vote |
| `GITHUB_REPO` | `owner/name` — no URL, no `.git` | submit + vote |
| `RESEND_API_KEY` | email provider key (O5) | notifications |
| `NOTIFY_FROM` | verified sender | notifications |
| `FACTORY_NOTIFY_SECRET` | long random string | notify guard + unsubscribe |
| `PUBLIC_BASE_URL` | site origin | unsubscribe links |

> ⚠️ **Redeploy after setting variables** — they are baked in at deploy
> time. The single most common setup miss.

## O5 — Email provider *(if notifications)*

Create the account, **verify the sender domain** (unverified senders fail
or land in spam), generate the key → env. Test end-to-end after O4's
redeploy, including that **the unsubscribe link works**.

## O6 — Schedules & per-schedule repo access *(Phase 5 — after the smoke test)*

Each role is one schedule firing its one-line activation prompt.
**Click-paths per runner, including each runner's traps, are in
[`RUNNERS.md`](./RUNNERS.md)** — hand the owner that file, don't
paraphrase it.

The one rule that outranks all others: **a scheduled session inherits no
access from anyone.** Each schedule must itself carry GitHub access —
the repo attached to it, or a token in its environment. The agent verifies
by firing each schedule once manually and confirming the role could list
issues (the role files report **BLOCKED** loudly if not; if a run says
"queue empty" while ideas exist, treat it as an access failure
mis-reported).

Cadence rules: interval ≥ run length; Coder offset 30–60 min after the
Architect; 6–24 h steady state (every firing is a billed agent session
even when idle). Pause switch: disable a schedule = pause a role.

## O7 — What CI costs *(installs using the CI-gate pattern)*

- GitHub-hosted **macOS runners bill at 10×** against the included
  minutes (Linux 1×); a ~1-minute iOS build ≈ 20 minutes-equivalent —
  a 2,000-minute allowance ≈ 100 builds/month; overage ≈ $0.08/min.
- GitHub may require a **payment method on file** before it schedules
  jobs at all, even with free minutes remaining.
- The generated verify workflow skips builds for changes that cannot
  affect compilation (docs, `ai-factory/**`) — that both saves money and
  creates the "no check is expected" case the CODER file handles.

## O8 — Ongoing operator duties

- Rotate tokens before expiry; update wherever they live; redeploy.
- **Watch the first weeks**: skim each Assessment and Report — you're
  checking the rulebook covers what you meant. Gaps become `RULES.md`
  edits (yours alone).
- Keep the repo private while any contact data lives in issue meta (W5).
- Revisit `WEAKNESSES.md` as usage grows; before going public, re-run the
  go-public checklist in the profile (bot account, injection surface,
  spam, vote integrity).

---

## Troubleshooting

### Jobs never start

| Symptom | Diagnosis |
| ------- | --------- |
| Jobs stuck `queued`, `runner_id: 0`, auto-cancelled after ~15 min — **on both Linux and macOS tiers** | Account-level or platform-level, never your code. **Check githubstatus.com first** — a platform incident looks exactly like a billing block (during one real incident, webhook delivery dropped to ~15% and pushes/PRs simply started no runs). Then check Billing → Actions (spending limit, no card on file). Cancelled runs are not revived by fixing billing — re-fire them. |
| PR opened but no check appears (outside an incident) | The workflow's triggers or path filters skipped it — by design for docs-only changes; the CODER file's decision table covers it. |

### API errors

| Error | Likely cause |
| ----- | ------------ |
| `Bad credentials` (401) | Token truncated/expired/revoked. |
| `Resource not accessible` / 403 | Missing permission on a fine-grained token, or the collaborator invite was never accepted, or the session's runner has no repo attached. |
| `Not Found` (404) | Wrong token *kind* for the ownership situation (see O3's table), wrong `owner/name`, or unaccepted invite. |
| Role reports "queue empty" but ideas exist | Access failure mis-reported (pre-R0 role files) or pick-order filter bug — treat as BLOCKED and check access from the runner. |

### Environment quirks

| Symptom | Note |
| ------- | ---- |
| Agent cannot delete a remote branch | Some managed agent environments block ref deletion. Harmless — delete stale branches yourself in the UI (repo → Branches). |
| `git` works but the API returns 403 from the agent | Git and API travel different channels in proxied environments; never infer one from the other (R0). |
| Emails not arriving | Sender domain unverified (O5), key missing in the *deployed* env (O4 redeploy!), or the issue is labeled `unsubscribed`. |
| Coder can't merge its own PR | Branch protection requires reviews — see O2. |
