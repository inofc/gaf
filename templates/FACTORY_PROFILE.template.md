# 🗂 Factory Profile — {PROJECT_NAME}

<!-- template-note
The single source of every project-specific value: templates + this
profile = the installed Factory. The agent pre-fills "Discovered" in
Phase 0 and fills the rest with the owner in Phase 1. No field empty when
Phase 3 starts; defaults marked "(default — revisit)". Decisions made
AFTER Phase 1 closes are appended as new, dated W-blocks (W16, W17, …) —
never silent edits to answered blocks; the profile is the installation's
memory. Delete template-notes on instantiation.
-->

**Setup shape (Phase 0.a):** {Minimal — tracker-direct, no board/voting/email
| Community — web board, optional voting + notifications}

## Discovered by the agent (Phase 0)

| Field | Value |
| ----- | ----- |
| Repository | `{OWNER}/{REPO}` |
| Default branch | `{DEFAULT_BRANCH}` |
| Repo visibility | {public \| private} |
| Stack | {STACK} |
| Version manifest | `{MANIFEST_PATH}` — {note EVERY location the version appears; some manifests repeat it per build configuration and all copies must move together} |
| Key source files / layout | {FILE_MAP — the files the Architect names in sketches; also seeds the area:* labels} |
| Current deploy mechanism | {DEPLOY_TODAY} |
| Verify commands pass today? | {yes/no, checked YYYY-MM-DD — or: "agent cannot run the toolchain ({WHY}); CI-gate pattern adopted; baseline = first CI run on unmodified {DEFAULT_BRANCH}"} |

## Decided with the owner (Phase 1)

### W1 — Product & idea scope
- **Product description:** {…}
- **Ideas in scope:** {…}
- **Ideas out of scope:** {… — topics that are owner-gated rather than
  banned should say so and point at their rule number}

### W2 — Protected invariants (→ RULES.md Section A)
> RULES.md is canonical for numbering: **A1–A7 are reserved** by the
> template; owner-specific invariants below are numbered **from A8**.
{INVARIANTS — numbered A8+, plus any product-specific rewording of A1–A7;
owner-gated rules (assess publicly, always decline, owner promotes by
hand) are marked as such}

### W3 — Tech scope
- **The stack is:** {TECH_SCOPE}
- **Dependency policy:** {DEPENDENCY_POLICY}

### W4 — Content policy
{CONTENT_POLICY}

### W5 — Privacy posture
- **Collect submitter emails:** {yes \| no}
- **Email store:** {none \| issue-meta-private-repo \| private-store}
- **Notes:** {…}

### W6 — Verification
- **Verify:** {exact local commands — or the CI-gate: workflow name, what
  the check proves, and that "verify passes" means "the check run
  concluded success"}
- **Manual verification means:** {what a human does on a device/in a
  browser — no role can do this; it is why W8 gates big changes}

### W7 — Definition of shipped
{SHIP_DEFINITION — one of: auto-deploy-on-merge / ci-deploy-on-merge /
merge-is-the-finish-line-human-releases / deployer-role, + platform
details. For app-store products state plainly: the Factory ships code to
{DEFAULT_BRANCH}; releasing to users is the owner's, and no role may
claim "live for users".}

### W8 — Pipeline shape
- **Pipeline:** {e.g. "Architect → Coder"}
- **Human gate:** {none \| partial: impact:major stops at the PR for
  {WHO}, max wait {N} days then one reminder \| full}
- {If roles run under the owner's identity: "the gate is convention, not
  enforcement — branch protection cannot bind the repo owner. Accepted."}

### W9 — Intake
- **Mode:** {tracker-direct \| board}
- **Board host / theme (if board):** {…}

### W9b — Voting
- **Voting:** {none — pick order is priority-then-oldest \| reactions —
  👍 count \| board votes}
- **Resulting pick order (verbatim, for the role files):** {…}

### W10 — Notifications
- **Enabled:** {yes \| no}
- **Provider / sender (if yes):** {…}

### W11 — Cadence (cron, UTC)
- **Architect:** {…}
- **Coder:** {… — offset 30–60 min after the Architect}
- {Bring-up cadences flagged "(bring-up — revisit)"; note: every firing
  is a billed agent session even on an empty queue}

### W12 — Voice & identity
- **Identity:** {bot account {NAME} \| owner's account with role-attributed
  commits ("{PROJECT} Factory (Architect/Coder)") and a role banner on
  every comment}
- **Tone:** {…}
- **Name generator:** {on + theme \| off}

### W13 — Issue taxonomy *(optional — delete if declined)*
- **type:** {list, exactly one per idea}
- **area:** {label → file/surface map, from Phase 0}
- **flags:** {each flag + the role behaviour it triggers}

### W14 — Priority lanes *(optional — delete if declined)*
- **Lane:** {GitHub logins picked first} — identified by issue
  `user.login` only, never the meta display name. Priority orders the
  queue; it never exempts from rules. Label: {…}

### W15 — Roadmap & placeholder gate *(optional — delete if declined)*
- **Placeholder surfaces:** {list}
- **Gate rule number:** {A-number} — polish allowed, implementation
  declined pending owner design
- **ROADMAP.md generated:** {yes/no}

---

*Confirmed by the owner on {DATE}: "yes, that's my project."*

<!-- Post-setup decisions append below as dated W-blocks. -->
