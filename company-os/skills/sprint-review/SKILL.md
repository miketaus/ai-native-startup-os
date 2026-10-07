---
name: sprint-review
description: Close the cycle honestly — what shipped, what actually moved, what we learned, and what to change. Compares outcomes against the success condition predicted at planning time. Use at the end of a planning cycle.
---

# sprint-review — close the cycle honestly

The point is **learning**, not reporting. A review that only lists what shipped has taught nobody
anything.

> `setup` deletes this skill on profiles that plan continuously.

## Before anything

Read `PROFILE.md` § 2 (objective function), § 4 (sources, tripwires), § 5 (lanes), § 7 (cycle).
**Read the cycle's plan** — specifically the **success condition predicted at planning time.**

**Run `state-sweep` first**, so the numbers are current rather than remembered.

> **Figures: read, don't recall.** Every metric here comes from a § 4 path opened in this run. This is
> the skill where recalled figures do the most damage — a cycle judged against a misremembered
> baseline teaches the wrong lesson permanently.

## Run it

**1. Committed versus shipped.** Plainly, without softening. Partial counts as not shipped; say what
fraction and what's left.

**2. Did the metric move?** Against the **predicted** success condition, not a retrofitted one.
Four honest outcomes:

| Outcome | What it means |
|---|---|
| **Shipped and moved** | The bet was right. Say why, so it's repeatable. |
| **Shipped, didn't move** | The most valuable outcome. The hypothesis was wrong — that's real learning. |
| **Didn't ship** | An execution or estimation problem, not a hypothesis problem. Different fix. |
| **Moved, but we can't attribute it** | Be honest. `analytics-lens` adjudicates. Often it wasn't us. |

**3. Resist retrofitting.** If the metric didn't move, do not go looking for a different metric that
did. This is the single most common way a review destroys its own value. `analytics-lens` calls it
when it happens.

**4. Attribution honesty.** Seasonality, a concurrent campaign, a big customer landing, a competitor's
outage. Correlation presented as cause is the default failure. If we can't attribute it, say so.

**5. What did we learn?** Something that changes what we do next. "We shipped four things" is not
learning. "Activation isn't limited by onboarding friction — we removed three steps and it moved 0.4pt
(`DID`, `B`-grade source)" is.

**6. What surprised us?** Surprise is where the model of the business was wrong, and it's the highest-
value thing in a review. Ask for it explicitly; it doesn't volunteer itself.

**7. Convene a focused panel** — `analytics-lens` (did it really move, is the attribution honest),
`product-lens` (what does this mean for what we build next), `finance-lens` (what did the cycle cost
against what it returned), `cs-lens` (what did customers experience). These seats only.

**8. What changes next cycle?** A review that changes nothing was a status meeting.

## Output

```
CYCLE: <n> — <dates>

COMMITTED vs SHIPPED
  <n> committed · <n> shipped · <n> partial · <n> not started
  <the honest reason for the gap>

THE SUCCESS CONDITION — as predicted at planning
  <what we said would be true>
  <what is actually true — from § 4 source, with grade>
  OUTCOME: shipped-and-moved | shipped-didn't-move | didn't-ship | can't-attribute

ATTRIBUTION
  <confounds considered — seasonality, campaigns, big accounts, competitor events>
  <analytics-lens verdict on whether we can claim this>

WHAT WE LEARNED
  <something that changes what we do next — not a list of what shipped>

WHAT SURPRISED US
  <where the model of the business was wrong>

COST vs RETURN
  <finance-lens, from § 4 with grade>

CUSTOMER EXPERIENCE
  <cs-lens — what landed differently for people who already pay us>

CHANGES FOR NEXT CYCLE
  <specific — process, sizing, sequencing, or what we stop doing>

PANEL
  <lens> → <one line each, including skipped seats and their blind spots>

NOT AUTOMATED
  Nothing here closed the cycle in the tracker or told anyone. That's yours.
```

## Rules

- **Never retrofit the success condition.** Judge against what was predicted. If it wasn't predicted,
  say the cycle wasn't testable and fix that at the next `sprint-plan`.
- **"Shipped, didn't move" is a good review.** Treat it as learning, not failure — a team that can't
  say it starts quietly choosing safe work.
- **Grade every figure.** With its § 4 source.
- **No blame.** Patterns, not people. A review people dread stops being honest, and then it's worthless.
- **Propose-only.** Closing the cycle and communicating results are the operator's (`PROFILE.md` § 8).
