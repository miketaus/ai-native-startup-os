---
name: review-panel
description: Review a specific code change or technical design with the technical lenses — architect, security, ops, ux, compliance, red-team. Use for a diff, a PR, or a design doc. For a business or strategy decision, use agent-panel; for verifying a change against its acceptance criteria, use qa.
---

# review-panel — review a change

> **Code profiles only.** `setup` deletes this skill when the profile ships no code.
>
> **Not `agent-panel`** — that's for business decisions. **Not `qa`** — that verifies a change does
> what it was supposed to; this judges whether it should exist in this shape.

## Before anything

Read `PROFILE.md` § 6 (repos, stack, test, deploy, branch/PR convention), § 9 (sensitive surface),
§ 4 (work tracking). Then `reference/gates.md`.

**Establish what changed.** `git diff`, the PR, or the design doc. If you can't see the actual change,
say so and stop — reviewing a description of a change finds nothing.

> **Read the code, don't recall it.** Open the files. Follow the call path. If the code contradicts the
> docs or `PROFILE.md`, **the code wins**, and that gap is a finding worth reporting on its own.

## The seats

| Lens | Brings |
|---|---|
| `architect` | Fit with existing patterns, coupling, one-way doors, dependencies, blast radius |
| `security-lens` | Authz gaps, input handling, secrets, exposure paths, exploitability |
| `ops-lens` | Day-2 owner, recurring load, what breaks at 10×, detection |
| `ux-lens` | Whether a person can complete the task — **only if the change is user-facing** |
| `compliance-lens` | **Mandatory.** Runs its own criteria test; can veto `ship` |
| `red-team` | The load-bearing assumption; ranked failure scenarios |

`compliance-lens` runs on **every** review, not only when someone flags a change as sensitive —
under-declaring is the common failure, which is exactly why it tests the criteria itself.

Spawn in parallel, independently. Name skipped seats and the blind spot each creates.

## Output

```
CHANGE: <what it does, one sentence> — <files/PR>

VERDICT: ship | ship-with-changes | fix-first | rework
          ← cannot be `ship` if compliance-lens VETOED (see PROFILE § 9 posture)
CONFIDENCE: high | medium | low
COMPLIANCE: clear | clear-with-conditions | RECOMMEND-AGAINST | VETO — <reason + posture>
SENSITIVE SURFACE: <engaged | not — per compliance-lens's own criteria test>

MUST FIX BEFORE MERGE
  <file:line> — <what's wrong, why it matters, and the fix>

SHOULD FIX
  <file:line> — <the same, lower stakes>

ONE-WAY DOORS
  <schema, public API, dependency, migration — what becomes hard to reverse>

SECURITY
  <vulnerabilities (exploitable now) / weaknesses / hygiene — from security-lens, ranked>

DAY-2
  <owner, recurring load, what breaks at 10×, how we'd detect failure>

TESTS
  <what's covered, what isn't, whether the § 6 standard is met>
  <if you ran them, the actual output; if you didn't, say so — never imply a test you didn't run>

RED-TEAM
  <load-bearing assumption; ranked failure scenarios>

PANEL
  <lens> → <verdict, one line, with bias check>
  <skipped seats + blind spot>

GATED — YOURS TO DO
  Merging and deploying are yours (`PROFILE.md` § 8).
```

## Rules

- **Read the actual diff.** A review of a description is theatre.
- **`file:line` on every finding.** A finding you can't point at isn't actionable.
- **Separate must-fix from should-fix.** A flat list of thirty items gets skimmed and nothing gets
  fixed. Rank ruthlessly.
- **Distinguish wrong from "not how I'd do it."** Only the first is a finding — say when you're
  expressing preference.
- **Never claim a test you didn't run.** If you ran the § 6 test command, paste the real output. If
  you didn't, say so and say who has to.
- **Propose-only.** Merges and deploys are the operator's (`PROFILE.md` § 8).
