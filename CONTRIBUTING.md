# Contributing to GAF

GAF is a **pattern**, and this repository is the installable skill that sets
one up. That shapes what a good contribution looks like: the bar is not "does
this read well" but **"did this earn its place in a real installation?"**

## The one rule

> Every rule in this repository exists because of a real success or a real
> failure in a running Factory.

Please keep it that way. A proposed rule, phase, or template change is much
easier to accept when it comes with the story: what was installed, what went
wrong (or right), and what the change would have prevented. Speculative
hardening — "this seems safer" — is the one thing this repo has deliberately
avoided, because unverified advice is what makes setup guides rot.

If you hit something real, that *is* the contribution. Open an issue with
what happened, even if you don't have a fix.

## Where things belong

| Change | File |
| ------ | ---- |
| A new setup step, or a phase reordering | `SKILL.md` |
| Something true of **every** Factory (data model, label grammar, design rules) | `references/BLUEPRINT.md` |
| A role's contract, or a new role recipe | `references/ROLES.md` |
| A new owner decision to ask about | `references/OWNER_WORKBOOK.md` |
| A human-only step, trap, or cost note | `references/OPERATOR_GUIDE.md` |
| A new runner, or a runner's trap | `references/RUNNERS.md` |
| A substitution for tracker / deploy / email / runner | `references/ADAPTATION.md` |
| A limitation we are knowingly deferring | `WEAKNESSES.md` |
| Anything generated into a target project | `templates/` |

Two structural invariants that are easy to break by accident:

- **`RULES.template.md` numbering is permanent.** `A1`–`A7` are reserved and
  owner-specific invariants start at `A8`. Roles cite rule numbers in public
  assessments, so renumbering breaks every citation in every installation
  that already exists. Add, never renumber.
- **Templates must pass the cold-read test.** An agent with zero context
  beyond the single instantiated file — and no assumption about which tools
  are available — has to be able to execute the role from it.

## Proposing a change

1. Open an issue describing the situation before writing a large PR; the
   discussion is usually where the real rule gets found.
2. Keep the diff scoped to one idea.
3. If your change alters the pattern rather than one installation, update the
   blueprint **and** the templates, so future Factories inherit it.
4. Match the existing voice: direct, second person, no filler. Where a rule
   is non-obvious, say in one clause *why* it exists.

## Scope

This repo intentionally contains no product code — only the skill, its
references, and its templates. Board implementations, role runners, and host
projects live in the projects that install a Factory.

## License

By contributing, you agree that your contributions are licensed under the
[Apache License 2.0](./LICENSE).
