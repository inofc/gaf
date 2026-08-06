# 🏰 The {PROJECT_NAME} AI Factory

<!-- template-note
Instantiate as ai-factory/README.md — the installed Factory's runbook and
the home of the activation prompts the owner's schedules fire. Fill from the
profile: {PROJECT_NAME}, {IDEA_SOURCE} (W9: "the in-product Ideas Board
(<route>)" or "issues filed directly in this repository"),
{PIPELINE_DIAGRAM}/{ROLE_TABLE} (W8 — extend for extra roles),
{SHIP_DEFINITION} (W7), {DEFAULT_BRANCH}, cadences (W11), env vars (only if
web board / notifications chosen), {BOT_NAME} (W12). Delete all
template-note blocks.
-->

This folder is the autonomous pipeline that turns community ideas into
shipped features. Users submit ideas via {IDEA_SOURCE}; each idea becomes an
issue in this repository; independently scheduled AI roles then move it
through review and build with no human in the loop. <!-- template-note:
adjust the last clause if W8 sets a human gate. -->

## How an idea flows

```
User submits ({IDEA_SOURCE})
   → Issue          labels: idea, stage:submitted            ── community votes here
   → AI Architect   labels: architect:approved (+ impact:*) + coder:queued
                    | architect:declined
                    posts a mandatory "Architect Assessment" comment
   → AI Coder       labels: coder:queued → coder:in-process → coder:executed
                    implements on a factory/* branch, posts a mandatory
                    "Implementation Report" comment
   → {SHIP_DEFINITION — e.g. "new version live (main auto-deploys to Vercel)"}
```
<!-- template-note: extend the diagram for Tester/Deployer pipelines. -->

The issue is the single source of truth. Approved ideas are also recorded in
[`CASES.md`](./CASES.md) — the durable ledger.

The Architect's verdict is final: on approval it adds `coder:queued` itself,
which is the Coder's work queue. There is no owner sign-off step anywhere in
the pipeline. <!-- template-note: replace with the gate description if W8
sets one. -->

## The roles

Each role's complete instructions live in a file in this folder, so the
activation prompt for a scheduled run is a one-liner that just points at it:

| Role | Instruction file | Activation prompt |
| ---- | ---------------- | ----------------- |
| 🏛 Architect | [`ARCHITECT.md`](./ARCHITECT.md) | "You are the AI Architect of the {PROJECT_NAME} AI Factory. Read the file `ai-factory/ARCHITECT.md` in the repository {OWNER}/{REPO} and follow it exactly, including its access check. Ignore any session-default working branch — you are authorised to work against `{DEFAULT_BRANCH}` as the file describes. One idea per run. Report 'queue empty' only after successfully listing the issues and finding nothing; if you cannot reach the tracker, report BLOCKED." |
| 🛠 Coder | [`CODER.md`](./CODER.md) | "You are the AI Coder of the {PROJECT_NAME} AI Factory. Read the file `ai-factory/CODER.md` in the repository {OWNER}/{REPO} and follow it exactly, including its access check. Ignore any session-default working branch — you are authorised to branch `factory/*` off `origin/{DEFAULT_BRANCH}`, push, open PRs, and ship as the file describes. One idea per run. Report 'queue empty' only after successfully listing the issues and finding nothing; if you cannot reach the tracker, report BLOCKED." |
<!-- template-note: one row per extra role. Prompts NAME the repository —
a scheduled session starts with no context and must know where to go. -->

Both roles process **exactly one idea per activation** and stop — bounded,
predictable runs. Editing how a role behaves means editing its instruction
file, not any schedule or prompt.

All roles are bound by the shared rulebook [`RULES.md`](./RULES.md) — the
protected invariants and privacy guardrails that outrank community votes.
The Architect enforces it at review time, downstream roles re-check it at
their own stage, and **only the owner edits it**.

## Running the roles

Each role is its own scheduled co-routine with its own cadence and,
optionally, its own AI model. Any runner that can execute an agent against
this repo works, because the prompt only points at the instruction file
(runner options: a scheduled agent trigger, a recurring local loop, or a CI
cron — see the GAF operator guide).

- **Schedules:** Architect {ARCHITECT_SCHEDULE}; Coder {CODER_SCHEDULE}
  (offset 30–60 min after the Architect so fresh approvals build in the same
  cycle).
- **Choose intervals longer than a run.** A firing never interrupts a run in
  progress — too-short intervals just stack useless back-to-back runs.
  Architect runs take minutes; builds can take 15–60+ minutes. A run that
  dies mid-build is auto-recovered by the stale-claim check (24 h) on a
  later run.
- **Session branches don't matter:** sessions may auto-create their own
  working branch — the roles never use it. All Factory git activity targets
  `origin/{DEFAULT_BRANCH}` exactly as the instruction files describe.
- **Pause switch:** disabling a role's schedule pauses that role; nothing in
  the repo needs to change. Disable all schedules to stop the Factory
  entirely.
- Run every role under the dedicated **{BOT_NAME}** bot identity so
  authorship stays consistent across submission → review → code. The
  shipping role additionally needs merge rights on `{DEFAULT_BRANCH}`.

## Label vocabulary

| Label | Meaning |
| ----- | ------- |
| `idea` | Marks an issue as a community idea (boards list only these). |
| `stage:submitted` | New, collecting votes, awaiting the Architect. |
| `architect:approved` | Architect approved it for building (assessment in a comment). |
| `architect:declined` | Architect declined (reason in a comment + `declineReason` meta). |
| `impact:minor` | Impact rating: isolated change. |
| `impact:moderate` | Impact rating: several files or a new feature surface. |
| `impact:major` | Impact rating: touches core logic / architecture / balance. |
| `coder:queued` | Set by the Architect on approval — the Coder's work queue. |
| `coder:in-process` | Coder is implementing it. |
| `coder:executed` | Built and shipped. |
| `unsubscribed` | Submitter opted out of notifications *(only if W10 on)*. |
<!-- template-note: add rows for extra roles' label triples, the W13
taxonomy groups (type:/area:/flag:), and the W14 priority label — the
full manifest lives in ai-factory/labels.json, synced by the
factory-labels workflow. -->

Each issue body ends with a **visible, machine-managed metadata block**
(shown indented here; not indented in real issue bodies):

    ---

    ### 🤖 AI Factory data (machine-managed — do not edit)

    ```json
    {"v":1,"votes":0,"author":"<display name>","imageUrl":null,
     "architectNote":"","declineReason":"","coderNote":"","version":""}
    ```

<!-- template-note: include the "email" field ONLY if W10 notifications
are on; a field no code reads invites drift. The votes field stays in
every install (even voting:none) so a later voting upgrade needs no
migration. -->
`votes` is updated by the vote endpoint (board voting) or read from 👍
reactions (reactions voting) or dormant (no voting — see W9b). The Architect sets `architectNote` /
`declineReason`; the Coder sets `coderNote` / `version` — those surface on
the board cards. **`email` is never returned to the browser** by any API (it
is, however, visible to repo collaborators in the issue body — one more
reason this repo stays private while real emails are collected).

> The block is visible **by design**: AI-tool layers strip hidden content
> (HTML comments) from issue bodies, so an invisible block would be
> unreadable — and destructible — by the AI roles.

<!-- template-note: keep the two sections below only if notifications (W10)
/ the web board (W9) were chosen; otherwise delete them. -->

## Notifying submitters

`POST /api/notify` (server-to-server, needs header `x-factory-secret`):

```
{ "ideaId": 42, "event": "architect-approved", "message": "…", "version": "v0.4.0" }
```

Events: `votes`, `architect-approved`, `architect-declined`, `coder-status`,
`version-shipped`. The endpoint reads the email from the issue meta and
skips anyone labeled `unsubscribed`. Both instruction files include the
exact call.

## Environment variables

| Var | Used by | Purpose |
| --- | ------- | ------- |
| `GITHUB_TOKEN` | all `/api/*` | The bot's **classic** token with `repo` scope. |
| `GITHUB_REPO` | all `/api/*` | `owner/name` of this repository. |
| `RESEND_API_KEY` | `/api/notify` | Email provider API key. |
| `NOTIFY_FROM` | `/api/notify` | Verified sender address. |
| `FACTORY_NOTIFY_SECRET` | `/api/notify`, unsubscribe | Shared secret guarding notify + signing unsubscribe links. |
| `PUBLIC_BASE_URL` | `/api/notify` | Site origin for building unsubscribe links. |

The role runners additionally need `FACTORY_NOTIFY_SECRET` and
`PUBLIC_BASE_URL` in *their* environment to call `/api/notify` (they skip
notifications gracefully when unset).
