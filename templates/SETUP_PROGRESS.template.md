# 🧭 Factory Setup Progress — {PROJECT_NAME}

<!-- template-note
Instantiate as ai-factory/SETUP_PROGRESS.md at the very start of setup
(Phase 0.a). The agent ticks items as they complete and commits+pushes at
every phase boundary — this file plus FACTORY_PROFILE.md are the complete
resume state: any fresh session can continue an interrupted setup from
the repo alone. Delete this note on instantiation; delete the file's
"how to resume" line only when setup is fully complete.
-->

**Setup shape:** {Minimal | Community}   ·   **Started:** {DATE}
**How to resume:** read `.claude/skills/gaf-setup/SKILL.md`, then this
file top to bottom — the first unticked box is where setup continues.

## Phase 0.a — Briefing
- [ ] Owner briefed with the wizard card (all phases, plain words)
- [ ] Setup shape chosen and recorded in the profile

## Phase 0 — Orient
- [ ] BLUEPRINT.md and ROLES.md read
- [ ] Repo inspected: stack, version manifest (all copies located),
      file map, deploy mechanism, default branch, visibility
- [ ] Verify commands run — or CI-gate pattern adopted (which: ______)
- [ ] Profile created and pre-filled · committed & pushed

## Phase 1 — Owner's Workbook
- [ ] W1–W3 answered · **cross-checked for contradictions**
- [ ] W4–W8 answered
- [ ] W9 intake · W9b voting · W10 notifications answered
- [ ] W11 cadence · W12 voice answered
- [ ] Optional blocks offered: W13 taxonomy [ ] · W14 priority [ ] ·
      W15 roadmap/placeholder gate [ ]
- [ ] Profile read back — owner said **"yes, that's my project"**
      · committed & pushed

## Phase 2 — Prerequisites
- [ ] Applicable operator steps determined from the profile
- [ ] Credentials created (list: ______)
- [ ] **Access verified from a test firing of the actual runner** —
      role could list issues (not from the setup session!)

## Phase 3 — Build
- [ ] labels.json generated (pipeline + enabled extras; area:* from the
      file map) · label-sync workflow instantiated · labels confirmed live
- [ ] RULES.md (A-numbering intact) · ARCHITECT.md · CODER.md ·
      CASES.md · README.md {· ROADMAP.md} instantiated — zero
      placeholders, cold-read clean
- [ ] CI gate instantiated (if applicable) + baseline run green on the
      unmodified default branch
- [ ] {Board + API built and deployed (Community shape)}
- [ ] Everything committed & pushed

## Phase 4 — Smoke test (through the production runner!)
- [ ] Test idea filed (small, impact:minor)
- [ ] Architect fired via runner — all checklist items green
- [ ] Coder fired via runner — all checklist items green
- [ ] Owner confirmed the change works in the live product

## Phase 5 — Go autonomous
- [ ] Schedules created per RUNNERS.md click-paths, offsets correct
- [ ] Per-role models set (Coder = strongest)
- [ ] Keys handed over: activation prompts · pause switch · RULES.md is
      the steering wheel · WEAKNESSES.md is the backlog
- [ ] **Setup complete** — {DATE}
