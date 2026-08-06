# 🧑‍🔧 GAF Operator Guide — the human-only steps

Some parts of a Factory can only be done by a person: creating accounts,
generating tokens, entering secrets into dashboards, verifying an email
sender, creating schedules. This guide walks the operator (usually the
project owner) through each one, in order, with the agent verifying every
step before setup continues. It is written for GitHub + Vercel + Resend —
the proven reference stack; substitutions are in
[`ADAPTATION.md`](./ADAPTATION.md), and the *shape* of every step (identity →
least-privilege access → credential → configuration → verify) is the same on
any platform.

Everything here descends from a live installation's setup notes — including
the traps, which were all hit for real.

---

## O1 — Create the Factory bot account

Use a **dedicated machine account** for the Factory's credentials — not your
personal account. Every idea-issue, vote edit, image commit, role comment,
and agent commit is authored by this identity, so it should *be* the Factory
— and its token has a tiny blast radius if it ever leaks.

1. Make a fresh email for it (a `+alias` on an existing inbox works).
2. Create the GitHub account. **Enable 2FA** and save the recovery codes —
   this account runs automation; don't get locked out of it.
3. In the bot's **Settings → Emails**, enable **"Keep my email address
   private"** so its commits use a `noreply` address.
4. Name it per the profile's W12 (`bot_name`) — the name appears on every
   public comment.

GitHub permits one machine/bot account per person on the free tier, so this
is within the rules.

## O2 — Grant Write access (not admin)

Keep the repo owned by your own account; add the bot as a collaborator.

- Repo → **Settings → Collaborators** → add the bot with the **Write** role.
- The bot receives an invite at its email and **must accept it** — a pending
  invite behaves exactly like no access, and is a classic source of
  mysterious 404s later.

**Write is all the Factory needs**: create issues, comment, label, commit
files, push branches, open/merge PRs. Admin would also allow changing
settings or deleting the repo — unnecessary risk.

> ⚠️ **Branch protection interaction:** if the repo requires PR reviews on
> the default branch, a lone Write collaborator cannot self-merge its own
> PRs — the reference ship definition ("Coder merges itself") breaks. Either
> exempt the bot, drop the requirement, or deliberately choose the
> human-gate ship definition (Workbook W7/W8) — but decide it, don't
> discover it.

## O3 — Generate the bot's token: a CLASSIC PAT

> ⚠️ **Do not use a fine-grained token for a collaborator setup.**
> Fine-grained PATs are scoped to a "resource owner" and **cannot reach a
> private repo owned by a different personal account**, even when the bot is
> an accepted collaborator — the API answers `404 Not Found`. Confirmed the
> hard way in the reference installation. (Fine-grained becomes viable only
> when the repo lives in an org the bot belongs to, or on the bot's own
> account.)

Logged in **as the bot**: Settings → Developer settings → **Personal access
tokens → Tokens (classic)** → Generate new token (classic).

- **Scopes:** the **`repo`** group (covers issues + contents on private
  repos — everything the Factory needs).
- Long expiry, or a calendar reminder to rotate. Copy the `ghp_…` token —
  GitHub shows it once.

**Verify (agent does this the moment you hand the token over):**

```bash
curl -s -H "Authorization: Bearer <TOKEN>" https://api.github.com/repos/<owner>/<repo> | head -5
# expect JSON with "id": … — not "Not Found" / "Bad credentials"
```

## O4 — Configure hosting & environment variables *(if web board, W9)*

Import the repo into the hosting platform (reference: Vercel, framework
preset auto-detected; the `/api` functions deploy automatically). Then in
**Project → Settings → Environment Variables** add:

| Name | Value | Required for |
| ---- | ----- | ------------ |
| `GITHUB_TOKEN` | the bot's classic PAT (O3) | submit + vote |
| `GITHUB_REPO` | `owner/name` — no URL, no `.git` | submit + vote |
| `RESEND_API_KEY` | email provider key (O5) | notifications |
| `NOTIFY_FROM` | verified sender, e.g. `Product <factory@yourdomain>` | notifications |
| `FACTORY_NOTIFY_SECRET` | a long random string you generate | notify guard + unsubscribe links |
| `PUBLIC_BASE_URL` | the site origin, e.g. `https://yourapp.example` | unsubscribe links |

Submitting and voting need only the first two; the rest can come later.
Enable each var for **Production** (and Preview if you test previews).

> ⚠️ **Redeploy after setting variables.** Env vars are baked in at deploy
> time; the deployment that existed before you added them was built without
> them. This is the single most common setup miss. Trigger a redeploy (push
> any commit, or Deployments → ⋯ → Redeploy), then test.

## O5 — Email provider *(if notifications, W10)*

1. Create the account (reference: Resend; alternatives in `ADAPTATION.md`).
2. **Verify the sender domain** (DNS records) — unverified senders either
   fail or land in spam.
3. Generate the API key → `RESEND_API_KEY`; set `NOTIFY_FROM` to the
   verified sender.
4. Test: after O4's redeploy, the agent submits a test idea with a test
   email and triggers a notify — confirm the mail arrives **and its
   unsubscribe link works**.

## O6 — Create the schedules *(Phase 5 — only after the smoke test passes)*

Each role is one recurring schedule whose prompt is the one-liner from the
generated `ai-factory/README.md`. Any runner that can execute an AI agent
against the repo works:

| Runner | How | Notes |
| ------ | --- | ----- |
| **Claude Code Routine / scheduled trigger** | cron trigger spawning a fresh session with the activation prompt | the reference runner |
| **Claude Code `/loop`** | recurring local loop, same prompt | good for supervised trial weeks |
| **GitHub Actions cron** | workflow running an agent action on schedule | also where a specific model per role is pinned |

Rules (from the profile's W11): cadence per role, **Coder offset 30–60 min
after the Architect**, never an interval shorter than a run, 3–24 h sensible
range. The runner needs the bot identity's credentials, and — for
notifications — `FACTORY_NOTIFY_SECRET` + `PUBLIC_BASE_URL` in *its*
environment (roles skip notifications gracefully when unset).

**Your controls, permanently:**
- **Pause switch** — disable a role's schedule to pause that role; disable
  both to stop the Factory. Nothing in the repo changes.
- **Manual run** — paste a role's activation prompt into any agent session.
- **Steering wheel** — edit `ai-factory/RULES.md`. Only you may.

## O7 — Ongoing operator duties (small, but real)

- **Rotate the bot token** before expiry; update it in hosting env +
  runners; redeploy.
- **Watch the first weeks**: skim each Assessment and Implementation Report.
  You're not approving — you're checking the rulebook covers what you meant.
  Gaps become `RULES.md` edits.
- **Keep the repo private** while real submitter emails live in issue meta
  (Workbook W5).
- Revisit `WEAKNESSES.md` (in the GAF skill folder) as usage grows — vote
  integrity and a test gate are the usual first upgrades.

---

## Troubleshooting

### "Server is not configured (missing GITHUB_TOKEN / GITHUB_REPO)."

The serverless function runs but can't see the env vars. **Always a hosting
configuration issue, never a code bug.** Check in order:

1. **Right project?** The vars must be on the project that serves your site.
2. **Right environments?** Enabled for Production (and Preview if used).
3. **Exact names/values.** Case-sensitive names; `GITHUB_REPO` is
   `owner/name` — no URL, no `.git`, no trailing spaces.
4. **Did you redeploy after adding them?** ⚠️ The most common miss — see O4.

### Errors after the config one is gone

| Error from the API | Likely cause |
| ------------------ | ------------ |
| `Bad credentials` (401) | Token pasted wrong (truncated/whitespace), expired, or revoked — generate a fresh one. |
| `Resource not accessible` / 403 | Token missing a permission, or the bot's collaborator **invite was never accepted**. |
| `Not Found` (404) | **A fine-grained token was used** (see O3 — switch to classic `repo` scope), or `GITHUB_REPO` doesn't match the real `owner/name`, or the invite was never accepted. |
| Emails not arriving | Sender domain not verified (O5), `RESEND_API_KEY` missing in the deployed env, or the issue is labeled `unsubscribed`. |
| Coder can't merge its own PR | Branch protection requires reviews — see the warning in O2. |
