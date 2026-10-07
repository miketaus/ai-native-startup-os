---
name: agent-panel
description: Convene the full lens panel on a decision, plan, strategy, or artifact — every active lens plus compliance-lens and red-team — and synthesize where they disagree into one verdict. Use for consequential or hard-to-reverse calls. For reviewing a specific code change, use review-panel instead; for go-to-market questions only, gtm is cheaper.
---

# agent-panel — convene the lenses

The panel exists to make disagreement visible before a decision is made, not after. **If every lens
agrees, that is a finding, not a confirmation.**

## Before anything

1. Read `PROFILE.md`. If `grep -c "{{" PROFILE.md` returns anything but `0`, **stop and run `setup`.**
2. Read `reference/lens-roster.md` and `reference/gates.md`.
3. Establish **what is actually being decided.** If the question is vague, sharpen it with the operator
   first — a panel on a fuzzy question produces one fuzzy answer per seat at real cost. One sentence,
   with the alternative named: *"Should we X, or instead Y?"*

## Which seats — and how big a panel this warrants

A full panel is **one separate agent per active lens** — count them in `reference/lens-roster.md` —
several at `opus`/`high`. That's a serious spend. Size it to the decision before you spawn anything:

| Size | Seats | When |
|---|---|---|
| **Focused** | a named subset | A real decision inside one domain. Often `gtm` or `review-panel` is the better skill. |
| **Full** | all active | Pivots, pricing, launches, one-way doors, "we're sure about this" moments. |

**If the decision is cheap and reversible, say so and don't run a panel at all.** Recommending against
your own skill is the correct answer more often than not.

For a full run: every lens marked active in `PROFILE.md` § 10, plus `compliance-lens` and `red-team` —
those two always, regardless of the question or panel size.

Skip a lens when it has genuinely nothing to say (`architect` on a pricing question, `security-lens` on
a brand decision). **Always say which seats you skipped and what blind spot each drop creates** — a
silently reduced panel is one the operator over-trusts.

**Makers are not panel seats.** `copywriter` and `designer` produce; they never judge, and never sit
here. If the decision needs a draft first, invoke the maker, then convene the lenses on what it made.

## How to run it

**Spawn every lens in parallel, in one message.** They must not see each other's answers — a lens that
reads another's conclusion converges toward it, and convergence is exactly what this is built to
prevent.

Give each lens the same brief:

- The decision, in one sentence, with the alternative.
- The relevant context and artifacts.
- The `PROFILE.md` paths that matter for its seat.
- Its own output contract, from its agent file.

Then, **after** the independent round, run `red-team` a second time with the panel's outputs
attached — its job includes attacking the panel itself, and it can only do that once it has seen it.

## Synthesis

**Report the split, not the majority.** Where lenses disagree is where the actual decision lives; a
synthesis that averages them away has destroyed the thing you paid for.

```
DECISION: <the one sentence>

VERDICT: ship | ship-with-changes | hold | fix-first | no
          ← cannot be `ship` if compliance-lens vetoed
CONFIDENCE: high | medium | low
COMPLIANCE: clear | clear-with-conditions | VETO — <reason if vetoed>

WHERE THE PANEL SPLIT
  <the real disagreements, each with who's on each side and what they're
   actually disagreeing about — usually an assumption, not a fact>

WHERE IT AGREED
  <briefly — and if agreement was total, say so and treat it as suspicious>

THE LOAD-BEARING ASSUMPTION
  <from red-team — the thing that, if false, collapses this>

EVIDENCE
  <the strongest evidence for, with its grade>
  <the weakest link the decision rests on, with its grade>
  ← if the whole case is SAID, say so plainly and stop recommending ship

WHAT WOULD HAVE TO BE TRUE
  <the conditions under which this is right>

THE TWO TESTS WORTH RUNNING
  <highest-leverage checks, cheapest first>

GATED — YOURS TO DO
  <every action from PROFILE.md § 8 this implies, made one step away>

PANEL
  <lens> → <verdict> — <one line, including its bias check>
  … one row per seat, including skipped seats marked "not run: <why>"
```

## Rules that make this work

- **Figures: read, don't recall.** Every number in the synthesis traces to a lens that opened a
  `PROFILE.md` § 4 path. If a file contradicts what you remember, the file wins — say so.
- **`compliance-lens` can veto.** If it did, the verdict cannot be `ship`. Record the reason verbatim.
  The operator may override, explicitly and in the open — never silently.
- **Preserve dissent.** A lens that disagreed with the outcome keeps its own row in the panel table.
  Never smooth a minority view out of the summary.
- **Declared biases are part of the answer.** When a lens's verdict follows its stated bias, say so —
  *"`finance-lens` says defer, and notes this is its conservative default; the downside here is capped
  at one week, so weight it accordingly."*
- **Propose-only.** The panel recommends. Everything in § 8 stays with the operator.
- **Cost.** A full panel is a real spend. Say what it cost in seats when you report.

## When not to use this

- **Cheap and reversible?** Just decide. A panel on a two-hour, undoable-in-a-day question is waste.
- **Reviewing a code change?** `review-panel` — narrower and cheaper.
- **Purely go-to-market?** `gtm` — the commercial seats only, aimed better.
- **Deciding what to work on next?** `triage` or `sprint-plan`.
- **A brand-new inbound idea?** `intake` first — it may not survive to need a panel.
