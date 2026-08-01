---
name: blockers
description: Find what is stuck and who is needed to unstick it, and produce the "needs you" list of decisions only the operator can make. Use for a standing sweep or when work feels stalled. For ranking what to do next, use triage.
---

# blockers — what's stuck, and who's needed?

Two outputs, and the second is the important one:

1. **What's blocked** and on whom.
2. **The "needs you" list** — decisions and actions only the operator can take. This is the thing that
   actually unsticks a company, and it's usually shorter than people expect and older than they'd like.

> **Not `triage`.** Triage ranks what's movable. Blockers finds what isn't.

## Before anything

Read `PROFILE.md` § 4 (work tracking, sources), § 5 (lanes and owners), § 7 (where the "needs you"
list goes), § 8 (gates — this list is largely made of gated items).

> **Figures: read, don't recall.** Read the current tracker state via the § 4 access pattern. Never
> report a blocker from memory of a previous run — half of them will have resolved.

## Run it

**1. Read the real state.** All lanes unless told otherwise. If you can't reach a source, **say which
one and stop reporting on it** — a partial sweep presented as complete is how blockers get missed.

**2. Find what's actually stuck.** Not just "not done." Stuck means: waiting on a decision, waiting on
a person, waiting on an external party, waiting on something that never got asked for, or in progress
with no movement for longer than the lane's normal cycle.

**3. For each, name the unblocking action and its owner.** A blocker without a named person is not a
finding, it's an observation. If nobody owns it, **that's the blocker** — say so.

**4. Age everything.** How long has this been stuck? Age is the strongest signal that something needs
escalation rather than patience. Sort the "needs you" list by age, oldest first — the old ones are old
because they're uncomfortable.

**5. Separate the four kinds.** They need different responses, and mixing them is why blocker lists
get ignored:

| Kind | Response |
|---|---|
| **Needs the operator** | A decision or a gated action only they can take. → the "needs you" list |
| **Needs someone else** | An internal person who hasn't been asked, or has been asked and hasn't answered |
| **Needs an external party** | Customer, vendor, regulator — track it, chase it, and note it may never resolve |
| **Needs nothing** | Not actually blocked. It's just not started. That's a triage problem, not a blocker |

**6. Find the silent blockers.** The ones nobody logged: a decision made verbally and never written
down, an approval everybody assumes happened, a dependency on a person who left, a thing waiting on
someone who doesn't know they're waited on. These don't appear in any tracker and are frequently the
oldest items in the company.

## Output

```
SWEPT: <lanes> — read from <§ 4 sources> — <"complete" or "PARTIAL: could not reach <x>">

NEEDS YOU — <n> items, oldest first
  1. <the decision or action> — stuck <duration> — <what unblocks on the far side>
     <one line of the context needed to decide, and your recommendation>
  2. …

NEEDS SOMEONE ELSE
  <item> — waiting on <person> — <duration> — <asked? y/n>   ← "never asked" is common

NEEDS AN EXTERNAL PARTY
  <item> — waiting on <who> — <duration> — <chased? realistic resolution?>

SILENT BLOCKERS — not in any tracker
  <what's stuck, why nobody logged it>

NOT ACTUALLY BLOCKED
  <item> — just not started → run triage

NOT AUTOMATED
  Nothing here notified anyone or updated a tracker. Every chase and decision below is yours.
  <If § 7 names a destination for this list, say it's a draft to paste there — not something sent.>
```

## Rules

- **The "needs you" list is the product.** Keep it short and decision-shaped. "Think about pricing" is
  not an item; "approve or reject the $2k/mo spend on X — recommendation: approve" is.
- **Give your recommendation on every operator decision.** A list of open questions with no
  recommendations pushes work back onto the person with the least time.
- **Age everything, and lead with the oldest.** Old blockers are old because someone is avoiding them.
- **Don't pad.** If three things are stuck, report three. A long list trains the operator to skim.
- **Propose-only.** Nothing here chases anyone or writes to a tracker. Say so every run.
