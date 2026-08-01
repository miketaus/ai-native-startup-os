---
name: qa
description: Verify a change actually does what it was supposed to, by exercising the real end-to-end path against the real target — not by inference. Use before calling something done. For judging whether a change should exist in this shape, use review-panel.
---

# qa — verify against the real path

**QA is a gate, not a build step.** Its only job is to find out whether the thing actually works.

> **Code profiles only.** `setup` deletes this skill when the profile ships no code.
>
> **Not `review-panel`** — that judges whether the change should exist in this shape. This checks
> whether it does what it claimed.

## The standard

> **Exercise the real end-to-end path against the real target.** Hit the live endpoint, run the real
> user flow, check the deployed bundle. **Do not infer success from a partial signal.** A green build
> is not a working feature; a passing unit test is not a working flow; "it should work now" is not a
> result.

This exists because "looks ready" is the most expensive phrase in software — a build that was green
but deployed to the wrong provider, an env var set but never applied, a dependency that only failed in
production.

When something fails, **surface the real error first.** Make the true exception visible and fix the
root cause. Never paper over it with a retry, a broadened catch, or a loosened assertion.

## Before anything

Read `PROFILE.md` § 6 — test command, **what "tested" actually means here**, lint, deploy target, CI.
Read the acceptance criteria: what was this supposed to do? If nobody wrote them, **derive them and
show your derivation** so the operator can correct it before you verify against the wrong thing.

> **Results: read, don't recall.** Every result reported here comes from a command you ran **in this
> run**. Never carry forward a pass from an earlier run, an earlier session, or CI — the code has
> changed since. If the output contradicts what you expected, **the output wins.**

## Run it

**1. Run the § 6 test command.** Paste the **actual output**, including failures. Never summarize a
test run you didn't perform.

**2. Run lint and typecheck** if § 6 names them. Real output.

**3. Exercise the real path.** The actual user flow, the actual endpoint, the actual built artifact,
against the environment the change targets. Say exactly which environment.

**4. Test the unhappy paths.** Empty input, bad input, missing permissions, network failure, partial
data, concurrent access, the back button, a second submit. This is where things actually break, and
where happy-path testing gives false confidence.

**5. Check what you might have broken.** What else touches this code or data? Verify the neighbours,
not just the change.

**6. Verify each acceptance criterion individually.** Pass or fail, with the evidence for each. Not
"looks good."

**7. Report honestly.** If you couldn't verify something — no access, no environment, no credentials —
say which criteria are **unverified** and who must check them. An unverified criterion reported as
passing is the worst possible output of this skill.

## Output

```
CHANGE: <what it was supposed to do>
TARGET: <the actual environment exercised>

RESULT: pass | fail | partial — <n> verified, <n> failed, <n> UNVERIFIED

ACCEPTANCE CRITERIA
  ✓ <criterion> — <the evidence: what you ran, what came back>
  ✗ <criterion> — <the real error, verbatim>
  ? <criterion> — UNVERIFIED: <why> — <who must check it>

TEST RUN
  $ <the § 6 command>
  <actual output, including failures>

LINT / TYPECHECK
  <actual output, or "not run: <why>">

REAL-PATH CHECK
  <what you actually exercised, step by step, and what happened>

UNHAPPY PATHS
  <empty · bad input · no permission · network failure · partial data · double submit>
  <which you tested; which you couldn't>

NEIGHBOURS CHECKED
  <what else touches this; what you verified>

FAILURES — root cause, not symptom
  <the real exception, and what's actually causing it>

NOT VERIFIED
  <everything you could not check, and who has to>
```

## Rules

- **Never report a pass you didn't observe.** This is the one rule that makes this skill worth running.
  If you can't verify it, it's `UNVERIFIED` — never `pass`.
- **Paste real output.** Summarized test results hide failures.
- **Real target, not a proxy.** Say which environment you exercised. If it was local and the change
  ships to production, say the production path is unverified.
- **Root cause before fix.** Surface the true error. Don't retry, don't broaden the catch, don't
  loosen the assertion.
- **QA never builds.** If it's broken, report it. Fixing is a separate act with its own review.
- **Propose-only.** Merging and deploying are the operator's (`PROFILE.md` § 8).
