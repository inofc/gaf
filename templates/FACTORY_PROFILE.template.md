# 🗂 Factory Profile — {PROJECT_NAME}

<!-- template-note
This file is the single source of every project-specific value in a GAF
installation: templates + this profile = the installed Factory. The setup
agent pre-fills the "Discovered" section in Phase 0 and fills the rest with
the owner during the Owner's Workbook session (Phase 1). Workbook block
numbers (W1–W12) are noted on each field. No field may be left empty when
Phase 3 (build) starts; conservative defaults are marked "(default —
revisit)" so they resurface at handover. Delete every template-note block
when instantiating.
-->

## Discovered by the agent (Phase 0)

| Field | Value |
| ----- | ----- |
| Repository | `{OWNER}/{REPO}` |
| Default branch | `{DEFAULT_BRANCH}` |
| Repo visibility | {public \| private} |
| Stack | {STACK — e.g. "React 19 + Vite, HTML canvas, Vercel serverless"} |
| Version manifest | `{MANIFEST_PATH}` (e.g. `package.json`) |
| Key source files / layout | {FILE_MAP — the files the Architect will name in sketches} |
| Current deploy mechanism | {DEPLOY_TODAY} |
| Verify commands pass today? | {yes/no, checked on YYYY-MM-DD} |

## Decided with the owner (Phase 1)

### W1 — Product & idea scope
- **Product description:** {PRODUCT_DESCRIPTION}
- **Ideas in scope:** {IDEAS_IN_SCOPE}
- **Ideas out of scope:** {IDEAS_OUT_OF_SCOPE}

### W2 — Protected invariants (→ RULES.md Section A)
{INVARIANTS — numbered list, ready to become A-rules; always includes the
four mandatory ones: core value sacred; end-to-end usability on supported
platforms; fixed tech scope; Factory-may-not-modify-itself + no
security-sensitive changes}

### W3 — Tech scope
- **The stack is:** {TECH_SCOPE}
- **Dependency policy:** {DEPENDENCY_POLICY}

### W4 — Content policy
{CONTENT_POLICY}

### W5 — Privacy posture
- **Collect submitter emails:** {yes \| no}
- **Email store:** {issue-meta-private-repo \| private-store \| none}
- **Notes:** {PRIVACY_NOTES}

### W6 — Verification
- **Verify commands:** {VERIFY_COMMANDS — exact commands, e.g. `npm run lint` + `npm run build`}
- **Manual verification means:** {MANUAL_VERIFICATION}

### W7 — Definition of shipped
{SHIP_DEFINITION — one of: auto-deploy-on-merge / ci-deploy-on-merge /
human-deploys / deployer-role, plus concrete platform details, e.g.
"Merging to `main` is shipping — Vercel auto-deploys `main`."}

### W8 — Pipeline shape
- **Pipeline:** {PIPELINE — e.g. "Architect → Coder" or "Architect → Coder → Tester"}
- **Human gate:** {none \| position + who + max wait}

### W9 — Intake
- **Mode:** {board \| tracker-direct}
- **Board host / theme (if board):** {BOARD_DETAILS}

### W10 — Notifications
- **Enabled:** {yes \| no}
- **Provider / sender (if yes):** {EMAIL_PROVIDER}, {SENDER_ADDRESS}

### W11 — Cadence (owner timezone: {TZ})
- **Architect:** {ARCHITECT_SCHEDULE — local + UTC cron}
- **Coder:** {CODER_SCHEDULE — offset 30–60 min after the Architect}
{EXTRA_ROLE_SCHEDULES — one line per extension role, or delete}

### W12 — Voice & identity
- **Bot account name:** {BOT_NAME}
- **Tone:** {TONE}
- **Name generator:** {on + theme \| off}

---

*Confirmed by the owner on {DATE}: "yes, that's my project."*
