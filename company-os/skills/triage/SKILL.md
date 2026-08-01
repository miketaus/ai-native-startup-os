---
name: triage
description: Rank the EXISTING open work in a lane by expected effect on the objective function, and propose the next few items. Use to answer "what should I work on." For deciding whether a new inbound idea becomes work at all, use intake instead.
---

# triage — what should we work on next?

**`triage` ranks work that already exists** in the tracker for one lane. It answers "of everything
open, what's next."

> **Not `intake`.** `intake` decides whether a new thing becomes work. If it isn't in the tracker yet,
> you want `intake`. **Not `blockers`** either — that finds what's stuck and who's needed.

## Before anything

Read `PROFILE.md` § 2 (objective function), § 4 (work tracking — **the exact field names and values**),
§ 5 (lanes, owners, levers). Then `reference/evidence-grades.md`.

**Ask which lane** if the operator didn't say. Triage is per-lane; ranking every lane at once produces
a list nobody can act on.

> **Figures: read, don't recall.** Read the current state of the tracker via the § 4 access pattern.
> Never rank from memory of what was open last time. If the tracker contradicts what you remember,
> the tracker wins.

## Run it

**1. Read the actual queue.** Everything open in the lane, using the § 4 field names. If you can't
reach the tracker, **say so plainly and stop** — a triage built on a guessed queue is worse than none,
because it looks authoritative.

**2. Rank by expected effect on the § 2 objective function**, within the viability constraint.
Not by age, not by who asked, not by what's nearly done. For each of the top items, name the lever.

**3. Break ties toward cheaper and faster.** When two items tie on expected effect, the smaller one
wins. Shipping resolves uncertainty that ranking can't.

**4. Flag items with no lever.** Anything that doesn't ladder to a § 5 lever is mis-scoped or shouldn't
be open. Say which, every run, until it's resolved or closed. This is how a backlog stays honest.

**5. Flag stale and zombie items.** Open a long time with no movement — either it matters and is
stuck (hand it to `blockers`), or it doesn't and should be closed. Both are actions; neither is
"leave it open."

**6. Name what you're NOT recommending and why.** The items just below the line, with the reason. That
list is usually more useful than the top three, because it's where the operator disagrees.

## Output

```
LANE: <name> — owner <name> — lever <the § 2 lever this lane moves>
READ FROM: <the § 4 source, and when — or "COULD NOT REACH: <why>">
QUEUE: <n> open

NEXT UP
  1. <item> — <lever> — <size> — <why now, one line, with evidence grade>
  2. …
  3. …

JUST BELOW THE LINE
  <item> — <why not now, one line>   ← the useful part; argue with it

NO LEVER — MIS-SCOPED OR SHOULDN'T BE OPEN
  <item> — <what's unclear>

STALE
  <item> — open <duration>, no movement — <close it, or it's blocked → run blockers>

NOT AUTOMATED
  Nothing here updated the tracker. Every rank, close, and reassignment below is yours.

GATED — YOURS TO DO
  <the specific tracker changes this implies>
```

## Rules

- **Rank by effect, not by effort or age.** A three-month-old item that moves nothing stays at the
  bottom, and should probably be closed.
- **Evidence grades on the reasoning.** "Do this next because customers asked (`SAID`)" is weaker than
  "because two accounts churned over it (`PAID`)" — make the difference visible in the ranking.
- **Never silently truncate.** If the queue is large and you ranked the top slice, say how many you
  didn't rank and on what basis you cut.
- **Propose-only.** Nothing here writes to the tracker. Say so explicitly every run — an operator who
  believes triage updated the board will stop checking it.
