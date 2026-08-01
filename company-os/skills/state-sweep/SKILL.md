---
name: state-sweep
description: Refresh the operating snapshot from the real sources — read every source of truth, draft an updated STATE.md, and flag what moved, what went stale, and what contradicts. Use on the standing cadence or before a planning session.
---

# state-sweep — refresh the operating picture

Reads every source in `PROFILE.md` § 4 and drafts an updated snapshot. This is the skill that keeps
every other skill honest: read-don't-recall only works if something actually re-reads.

## Before anything

Read `PROFILE.md` § 4 (**every** source, with grades and tripwires), § 5 (lanes), § 7 (cadence).

> **Figures: read, don't recall.** This skill is the reason that rule exists. Every number in the
> output comes from a source you opened **in this run**. Never carry a figure forward from a previous
> STATE.md without re-reading it — that's how a stale number becomes permanent.

## Run it

**1. Open every § 4 source.** Each one, in this run. Record what you read and when.

**2. Record what you could NOT read**, and treat it as a finding. A source that's unreachable, needs
credentials you don't have, or has moved is more important than most of the numbers you did get. Never
fill the gap with a previous value — mark it `STALE — last read <when>`.

**3. Check the tripwires first.** Anything on the § 4 tripwire list gets flagged rather than reported.

**4. Report each figure with its § 4 grade.** A `C`-grade number is a planning input, not a fact, and
everyone downstream needs to know which they're getting.

**5. Find what moved.** Against the previous STATE.md: what changed, by how much, and in which
direction relative to the § 2 objective function. Movement is the signal; levels are context.

**6. Find contradictions.** Where two sources disagree — pipeline in the CRM versus revenue in the
books, tracker status versus what actually shipped. **Contradictions are the most valuable output of
this sweep.** Report them prominently; do not reconcile them silently by picking one.

**7. Flag staleness.** Any source not updated within its expected cadence. A metric nobody has
refreshed in six weeks is a process finding, not a number.

**8. Draft the updated STATE.md.** As a draft. Show the diff.

## Output

```
SWEPT: <date> — <n>/<n> sources read

COULD NOT READ
  <source> — <why> — last known value <when>, marked STALE   ← or "all sources read"

MOVED SINCE LAST SWEEP
  <metric> — <from> → <to> — <grade> — <toward | away from the § 2 objective function>

CONTRADICTIONS  ← read these first
  <source A> says <x>, <source B> says <y> — <which is likelier right, and who resolves it>

TRIPWIRES HIT
  <figure> — flagged, not reported — <why it's known-bad>

STALE
  <source> — last updated <when>, expected <cadence>

STATE.md DRAFT
  <the diff against the current file>

NOT AUTOMATED
  This sweep ran because you started it. Nothing here wrote to STATE.md or notified anyone —
  the draft above is yours to commit.
```

## Rules

- **Never carry a figure forward without re-reading it.** If you couldn't read it this run, it's
  `STALE`, not a number.
- **Contradictions are the point.** Two sources disagreeing is the finding most worth the operator's
  attention, and the one most often smoothed away.
- **Grade everything.** Ungraded figures get treated as facts.
- **Propose-only.** Writing STATE.md is a draft handed to the operator unless § 8 says otherwise.
- **Say it isn't automatic.** Nothing here runs on a timer. If the operator believes this refreshes
  itself, they'll stop running it and quietly plan on months-old numbers.
