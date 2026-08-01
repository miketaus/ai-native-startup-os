---
name: sprint-plan
description: Plan the next cycle — pick what gets committed, size it honestly, name what it's betting on, and say what's explicitly not happening. Use at the start of a planning cycle. For ranking the open queue without committing, use triage.
---

# sprint-plan — commit the next cycle

> `setup` deletes this skill on profiles that plan continuously rather than in cycles.

Planning is a commitment, not a wish list. The output must be small enough to be true.

## Before anything

Read `PROFILE.md` § 2 (objective function), § 4 (work tracking, sources), § 5 (lanes and levers),
§ 7 (cycle length and owner). Then `reference/evidence-grades.md`.

**Run `state-sweep` first, or read STATE.md**, so the plan rests on current figures rather than what
was true last cycle.

> **Figures: read, don't recall.** Capacity, velocity, and current metric values come from § 4 paths.

## Run it

**1. Close the last cycle honestly** if `sprint-review` hasn't run. What was committed, what shipped,
what didn't and why. **A plan that ignores a pattern of over-commitment repeats it.**

**2. Establish real capacity.** From actual recent throughput, not from optimism. Subtract known
holidays, on-call, interviews, support load, and the maintenance tail `ops-lens` keeps naming.
**Then subtract more** — the honest number is almost always lower than the felt one.

**3. Name the cycle's one bet.** What is this cycle *for*? A cycle that's a list of unrelated items
achieves less than one with a theme, because nothing compounds. State it as a hypothesis:
*"we believe activation is limited by X; this cycle tests that."*

**4. Select the work**, per lane, each item naming its § 2 lever. Anything without a lever needs a
reason to be in the cycle — keeping-the-lights-on work is legitimate, but say that's what it is.

**5. Convene a focused panel** — `product-lens` (is this the right work), `analytics-lens` (will we be
able to tell if it worked), `finance-lens` (can we afford this cycle's shape), `ops-lens` (what
ongoing load does it create), `red-team` (what's this plan assuming). Five seats, not sixteen —
`/agent-panel` is for the strategy behind the plan, not the plan itself.

**6. State the success condition before the cycle starts.** What would have to be true at the end for
this to have worked — in metric terms, predicted **now**. A success condition written afterwards is a
story, not a test.

**7. Say what's explicitly NOT happening.** The most useful part of any plan. Name the things people
will expect and won't get, so the expectation is set now rather than discovered later.

## Output

```
CYCLE: <n> — <dates> — owner <name>

LAST CYCLE, HONESTLY
  Committed <n> · shipped <n> · <the pattern, if there is one>

CAPACITY: <real number, after subtractions> — <what was subtracted>

THE BET
  <one sentence: what this cycle is for, as a hypothesis>

COMMITTED
  <lane> — <item> — <lever> — <size> — <owner>
  …
  Total: <n> against capacity <n>

SUCCESS CONDITION — predicted now
  <what must be true at the end, in metric terms, with the § 4 source that will measure it>

EXPLICITLY NOT HAPPENING
  <the things people will expect and won't get>

PANEL
  product-lens → <verdict, one line>
  analytics-lens → <can we measure it?>
  finance-lens → <affordable?>
  ops-lens → <ongoing load created>
  red-team → <what this plan assumes; the likeliest way it fails>
  <skipped seats and the blind spot each creates>

RISKS
  <what could make this cycle fail, ranked, with early detection signals>

NOT AUTOMATED
  Nothing here updated the tracker or told anyone. Committing this cycle is yours.

GATED — YOURS TO DO
  <the specific tracker changes and the roadmap change, if any>
```

## Rules

- **Under-commit.** A cycle that finishes early is recoverable; one that finishes at 60% corrodes
  trust in every future plan.
- **Every item names a lever, or names why it doesn't.** Keeping-the-lights-on work is fine, labelled.
- **Predict before, not after.** The success condition is written at the start or it isn't a test.
- **Never plan on `SAID` alone.** If the cycle's bet rests only on what people said, say so and add
  the cheapest thing that would move it to `DID`.
- **Propose-only.** Roadmap changes and tracker commitments are the operator's (`PROFILE.md` § 8).
