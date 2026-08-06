# 🕹 GAF Runners — exact setup walkthroughs, per runner

The runner is whatever fires a role's one-line activation prompt on a
schedule. Because instructions live in the repo (R1), any runner produces
the same Factory — but each runner has its own setup clicks and its own
trap. This file gives the human operator **literal click-paths**; the
agent's job is to fill in the placeholders (prompt text, cron strings,
repo name), hand these steps over verbatim, and then **verify by firing a
test run** — never to assume the human knows the UI.

**The universal rule, regardless of runner:** a scheduled session
**inherits no access** from the setup session or from your logged-in
browser. Every schedule must itself be given GitHub access. This was the
single most expensive lesson of the reference installs — a role without it
doesn't error visibly, it just runs blind (the role files report
**BLOCKED** to make this loud).

---

## R-A — Claude Routines *(reference runner)*

One Routine per role. Each firing spawns a fresh agent session with the
Routine's instructions, connectors, and attached repositories.

**Create the Architect's Routine:**

1. Open **Claude → Routines → New routine**.
2. **Name:** e.g. `<Project> — AI Architect`.
3. **Instructions:** paste the Architect activation prompt from the
   generated `ai-factory/README.md` — nothing else.
4. **⚠️ THE TRAP — attach the repository.** In the instructions box's
   toolbar, use the **➕ / repository chip** and attach
   `<owner>/<repo>`. **This attachment is what grants the fired session
   its GitHub API access.** A Routine without it can still clone over
   git, but cannot list issues, label, or comment — the role will report
   BLOCKED. (Connector chips like `Claude_Code_Remote` are *not* a
   substitute; the repo chip is the one that matters.)
5. **Model** (dropdown in the instructions box): strongest available for
   the Coder's Routine; one tier down is fine for the Architect.
6. **Trigger:** *Add trigger → Schedule → Custom* → paste the cron from
   the profile (UTC). For the smoke test, create the Routine **without**
   a trigger (or paused) and use its *Run now* control instead.
7. Save.

**Repeat for the Coder**, with the Coder prompt and the offset cron
(30–60 min after the Architect — e.g. Architect `0 */6 * * *`, Coder
`30 */6 * * *`).

**Verify (agent):** fire each Routine once manually; the run must report
having listed the repo's issues (or a verdict/build), **not** BLOCKED,
and not "queue empty" while ideas exist.

**Pause switch:** the Routine's enable/disable toggle. Nothing in the
repo changes.

## R-B — Claude Code `/loop` *(supervised trial weeks)*

In a Claude Code session opened on the repo:
`/loop 6h <paste the activation prompt>` — the session re-runs the prompt
on the interval while it stays open. Access = the session's own, so the
universal rule is satisfied by the session itself; good for watching the
first days closely, not for unattended operation.

## R-C — GitHub Actions cron + agent action

Keeps everything in-repo; natural place to pin per-role models in config.

1. One workflow per role, `on: schedule:` with the profile's cron
   (**note:** Actions cron has no seconds field and fires best-effort,
   sometimes minutes late — fine for a Factory).
2. The workflow invokes your agent action with the activation prompt.
3. **Access:** the built-in `GITHUB_TOKEN` works for issues/labels/
   comments in-repo, but PRs it opens **do not trigger other workflows**
   (including the verify gate) — so give the agent step a real token
   (O3's table) stored as a **repository secret**, Settings → Secrets and
   variables → Actions.
4. Each firing consumes Actions minutes even on an empty queue — mind
   the cadence.

## R-D — Anything else

The contract a runner must satisfy: fires the one-line prompt on a
schedule · fresh execution each time · carries GitHub access usable from
inside the run (token env var, e.g. `FACTORY_GITHUB_TOKEN`, or
platform-native attachment) · respects one-idea-per-run · can be disabled
(that's the pause switch). Verify with one manual firing before
scheduling — always.

---

## Where secrets live, per runner

| Runner | Where the token goes |
| ------ | -------------------- |
| Claude Routines | Usually no token — the attached repo grants access. If a token is still needed, the environment's variable store, **never** the chat. |
| `/loop` session | The session's environment. |
| Actions cron | Repository secret → exposed to the job as an env var. |
| Web board's serverless functions | Hosting dashboard env vars (operator guide O4) — never in the repo, never in the browser. |

Role files should name the expected variable (reference:
`FACTORY_GITHUB_TOKEN`) and check it as route 2 of the R0 access check.

## Smoke-test rule (repeated here because it gets skipped)

Phase 4 runs **through these runners**, fired manually — never by pasting
an activation prompt into the setup agent's own chat session. The setup
session has its own access and its own context; a paste-test passes for
the wrong reasons and masks precisely the failures this file exists to
prevent.
