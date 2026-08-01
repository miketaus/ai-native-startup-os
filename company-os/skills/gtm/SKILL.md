---
name: gtm
description: Work a go-to-market question — positioning, launch, campaign, pricing, channel, or messaging — with the commercial lenses and, when a draft is needed, the copywriter. Cheaper and better-targeted than a full agent-panel for anything customer-facing.
---

# gtm — work a go-to-market question

Convenes the commercial seats. Use this instead of `/agent-panel` for anything about how the product
reaches and persuades customers — it's six seats rather than sixteen and better aimed.

## Before anything

Read `PROFILE.md` § 1 (identity), § 2 (objective function), § 3 (customer, personas, economic buyer,
quote grounding), § 4 (pipeline, product metrics, tripwires), § 5 (lanes). Then
`reference/evidence-grades.md`.

**Sharpen the question first.** "Help with marketing" produces six vague answers at real cost. Get to
something decidable: *"Should we lead the launch with the integration or the time saved?"*

> **Figures: read, don't recall.** Conversion, CAC, pipeline, traffic — from § 4 paths opened in this
> run. Check § 4 tripwires before citing anything.

## Make first, then judge — when a draft is needed

If the question needs actual copy or a layout, **invoke the maker before the lenses**:

- `copywriter` → three genuinely different drafts, each with its claims graded.
- `designer` → layout and structure, with states and tokens specified.

**Then the lenses judge what the maker made.** Never let a maker review its own output — that
separation is the whole reason the review is worth anything.

If the question is strategic rather than a drafting job, skip the makers entirely.

## The seats

| Lens | Brings |
|---|---|
| `brand-lens` | Does this sound like us; does it compound or spend reputation; are the claims supported |
| `growth-lens` | Will it move a lever; is the mechanism real; does the channel scale |
| `sales-lens` | Can it be sold, by whom, against what objection; who signs |
| `comms-lens` | Who hears it, when, in what order; what they'll actually hear |
| `customer-persona` | The unfiltered reaction — **only if § 3 is grounded**; it declines otherwise |
| `finance-lens` | What it costs, what it returns, what it does to CAC and runway |
| `red-team` | Whether this moves the objective function or a vanity proxy |

Add `compliance-lens` **whenever the work makes a public claim** — about security, privacy,
compliance, performance, or customer outcomes. That's most launches. It can veto a `ship`.

Spawn them in parallel, independently. Say which seats you skipped and what each drop blinds you to.

## Output

```
QUESTION: <the sharpened, decidable version>

VERDICT: ship | ship-with-changes | rework | hold
CONFIDENCE: high | medium | low
COMPLIANCE: <clear | conditions | VETO — reason> <or "not convened: no public claim made">

DRAFTS  <if a maker ran>
  <the options, with the recommended pick>

WHERE THE PANEL SPLIT
  <the real disagreements — usually growth vs brand, or sales vs cs, and always worth reading>

THE POSITIONING, IN ONE SENTENCE
  <the sentence a customer would repeat to a colleague — or "we can't write it yet, because…">

WHO IT'S FOR / WHO IT ISN'T
  <segment, and who we're deliberately not serving>

CLAIMS + EVIDENCE
  <each claim → grade → § 4 source, or "unsourced — cut or substantiate">

CHANNEL + MECHANISM
  <where it runs, why it would work, tactic or channel>

SEQUENCE  <from comms-lens>
  <who hears it, in what order, when>

HOW WE'LL KNOW
  <metric, expected effect predicted NOW, time to signal, § 4 source that measures it>

PANEL
  <lens> → <verdict, one line, including bias check>
  <skipped seats + blind spot>

GATED — YOURS TO DO
  Publishing, sending, spending, pricing changes, customer contact — all yours. The drafts
  above are one step away.
```

## Rules

- **Ungrounded `customer-persona` declines, and that's correct.** Don't work around it by asking
  another lens to imagine the customer. Report the decline and what would activate it.
- **Every claim carries a grade.** Unsourced numbers in customer-facing copy are a liability, and
  `brand-lens` and `compliance-lens` will both reject them.
- **Predict the effect before running.** Otherwise the result can't disconfirm anything.
- **Propose-only.** Publishing, posting, sending, spending, and pricing are the operator's
  (`PROFILE.md` § 8). Deliver the artifact finished; stop one step short.
