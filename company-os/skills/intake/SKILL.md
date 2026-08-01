---
name: intake
description: Assess a NEW idea, request, or inbound item that isn't tracked work yet — decide whether it becomes work at all, and at what size. Use when something arrives from outside the plan. For ranking work that already exists in the tracker, use triage instead.
---

# intake — should this become work at all?

**`intake` is for things that aren't work yet.** A customer request, a founder idea, an inbound
opportunity, a competitor move, something from a call. Most of these should not become work, and
saying so quickly is the value.

> **Not `triage`.** `triage` ranks items already in the tracker. `intake` decides whether something
> gets in. If it's already an issue, you want `triage`.

## Before anything

Read `PROFILE.md` § 2 (objective function), § 3 (customer), § 4 (sources, tripwires), § 5 (lanes).
Then `reference/evidence-grades.md`.

> **Figures: read, don't recall.** Any number used to size this comes from a § 4 path you opened.

## Run it

**1. State the request in one sentence, in the requester's terms.** If you can't, you don't understand
it yet — ask.

**2. Find the underlying problem.** The request is a proposed solution. What's the problem behind it?
Requests arrive as features; the useful version is the job the person is trying to do. Say both.

**3. Grade the evidence.** Who asked, how many, and what grade:

- One customer asking is `SAID` — and one customer.
- Five customers asking is `SAID` — and five customers. Volume doesn't upgrade a grade.
- A customer who churned over it is `PAID` evidence, and much stronger.
- A workaround someone built themselves is `STUCK`, and stronger still.

**4. Check it against the objective function.** Which § 2 lever would this move, and roughly how much?
Many good ideas are correct and too small to matter at this stage. Say so plainly — "this is a real
problem and it isn't worth solving now" is a legitimate and useful outcome.

**5. Check whether it already exists.** In the tracker, in the backlog, as something we decided
against before. Re-deciding a settled question is a common and expensive failure. If we said no
before, say what's changed.

**6. Assign a lane, or reject it.** Every item belongs to a lane from § 5, with an owner. Something
that fits no lane is either out of scope or reveals a missing lane — say which.

**7. Consult, narrowly.** `product-lens` on whether it's worth building, `analytics-lens` on whether
the claimed effect is measurable, and `customer-persona` **only if** § 3 is grounded. Don't convene
more than that — intake is a filter, not a panel. If the item turns out to be consequential and
hard to reverse, say so and recommend `/agent-panel` rather than deciding here.

## Output

```
REQUEST: <one sentence, in their terms>
UNDERLYING PROBLEM: <the job behind the request>

VERDICT: take | park | reject | needs-panel
LANE: <from § 5, with owner — or "fits no lane: <why that matters>">
LEVER: <which § 2 lever, and roughly how much — or "none">

EVIDENCE: <who asked, how many, grade — and what would upgrade the grade>
ALREADY EXISTS: <duplicate, or previously decided against + what's changed>
SIZE: <rough, in the § 4 sizing units — or "unknown, and here's what to check">

IF TAKE: <the smallest version that tests the riskiest assumption>
IF PARK: <the condition that would bring it back — be specific, not "revisit later">
IF REJECT: <the honest reason, in one sentence you could say to the requester>

GATED — YOURS TO DO
  <creating the tracker item, replying to the requester — both operator actions>
```

## Rules

- **Reject is the common answer, and that's healthy.** An intake that takes everything isn't a filter.
- **"Park" needs a trigger.** "Revisit later" is a rejection that nobody has to feel bad about. Write
  the actual condition: *"if two more customers on the enterprise plan ask, take it."*
- **Never plan on `SAID` alone.** If the whole case is what someone said, the verdict is `park` with a
  named cheap test — not `take`.
- **Propose-only.** Creating the tracker item and replying to the requester are the operator's
  (`PROFILE.md` § 8). Draft both; hand them over.
