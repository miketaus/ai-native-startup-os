# Reconciliation — playbook ⟷ operating harness

This repo now carries two things that were designed independently:

| | What it is | Where |
|---|---|---|
| **The playbook** | Prose operating model: north star, cascade, security, maturity phases | [`ai-native-startup-os.md`](./ai-native-startup-os.md) |
| **The harness** | A runnable Claude Code plugin: a self-configuring `PROFILE.md`, an interview, sixteen lens agents, two makers, twelve skills | [`company-os/`](./company-os/) |

The playbook is the **substrate** — what a company must decide. The harness is the **runtime** — what
Claude Code actually executes against those decisions. Roughly 80% of the value is in the substrate;
the harness is the 20% that makes it run.

Four genuine design collisions had to be resolved before they could live together — three head-on
contradictions and one category distinction. This document records the resolutions and the reasoning,
because each is load-bearing and will look like an arbitrary style choice to anyone who edits it later.

---

## Conflict 1 — `[BRACKET]` vs `{{PLACEHOLDER}}`

**The collision.** The playbook marks fill-me-in slots as `[ROLE / NAME]`. The harness needs a
machine-checkable "is this configured yet?" test, and `grep -c "\["` is useless in a Markdown file
full of links.

**Resolution — two markers with different meanings, not one convention imposed on both.**

| Marker | Means | Blocks? | Checked by |
|---|---|---|---|
| `{{PLACEHOLDER}}` | **`setup` must fill this before any other skill runs.** | Yes — hard block | `grep -c "{{" PROFILE.md` must print `0` |
| `[bracket]` | A human fills this in later, in prose. Non-blocking. | No | Human review |
| `TBD — open question` | Asked, genuinely unknown. Surfaced to the operator, never invented. | No, but reported | `setup` end-of-run report |

`{{` is used because it appears in no natural Markdown, no code fence in this repo, and no URL — so
the grep has zero false positives. `PROFILE.md` uses `{{…}}` exclusively. The playbook keeps
`[BRACKET]`, because it is a document a human reads, not a file a skill parses.

The third row matters as much as the first two. The failure mode this harness exists to prevent is a
confident answer resting on an invented fact, so "I don't know" must have somewhere to go that isn't
a guess.

---

## Conflict 2 — `north-star.md` + `tooling.md` + `STATE.md` vs one `PROFILE.md`

**The collision.** The playbook puts operating truth in several files in an `org-os` repo. The
harness wants one `PROFILE.md` that every skill reads. Merging them into one file would produce a
large, high-churn document that every agent re-reads every session, and — worse — would pull current
figures into the file prompts recite from. That is exactly the drift the harness is built to avoid.

**Resolution — split by volatility, not by topic. `PROFILE.md` is an index, not a replacement.**

```
PROFILE.md              STABLE   read every session   config + POINTERS   (small, ~200 lines)
    │ § 4 names the paths below; it never restates their contents
    ▼
north-star.md           SLOW     read when planning   vision · NSM · viability constraint
metrics/aarrr.md        SLOW     read when measuring  metric definitions, lever ownership
tooling.md              SLOW     read when routing    vault map, board map, connectors
source-of-truth/        FAST     read when reporting  STATE.md — the live snapshot, figures live HERE
```

**The rule that makes this work, repeated in every agent and skill file:**

> **Figures: read, don't recall.** Never state a current number from memory or from prompt text.
> Open the file named in `PROFILE.md` § 4 and read it. If a file contradicts what you remember,
> **the file wins** — and say so out loud.

So `PROFILE.md` may say *"runway is tracked in `finance/model.xlsx`, grade B, owner CFO."* It may
**never** say *"runway is 14 months."* A profile that names figures is a profile that will argue
confidently from stale numbers within a quarter.

**What this preserves from the playbook.** The `org-os` layout (§ 2.1) survives intact — `PROFILE.md`
sits above it and points into it. Nothing in the playbook's file structure had to be thrown away.

**What this changes.** The playbook's **Chief-of-Staff-agent-per-leader** becomes **lanes** in
`PROFILE.md` § 5. A lane is a workstream with an owner and one NSM lever, and the skills operate
per-lane. This generalizes the idea in both directions: a 40-person company gets one lane per
leader (identical to a CoS agent each), and a solo founder gets three lanes they own themselves —
without needing to pretend they have a leadership team. The CoS *behavior* is now spread across
`state-sweep`, `triage`, and `blockers` rather than concentrated in one per-leader agent file, so it
doesn't have to be duplicated six times and kept in sync.

---

## Conflict 3 — propose-only-everywhere (R10) vs `compliance-lens` blocking

**The collision.** The playbook's R10 says *all agents are propose-only behind a human gate*. The
brief says `compliance-lens` **can block on its own** and is mandatory, not advisory, for
sensitive-surface changes. Read literally, these contradict.

**Resolution — they are two different powers, and conflating them is the error.**

| Power | Who has it | What it means |
|---|---|---|
| **Act on the world** | **Nobody.** Not one agent, ever. | Publish, spend, merge, deploy, send externally, change the roadmap. Operator only — see `PROFILE.md` § 8. |
| **Veto a verdict** | `compliance-lens`, alone. | Can force the panel's verdict away from `ship`. Cannot cause anything to happen. |

Blocking a *recommendation* is not acting. `compliance-lens` withholding a `ship` verdict changes
what the panel tells the operator; it changes nothing in the world, and the operator remains free to
override it in the open. So R10 is restated rather than weakened:

> **R10 (restated).** Every agent is propose-only; none may act. One agent — `compliance-lens` — may
> additionally veto a `ship` verdict when the change touches the sensitive surface (`PROFILE.md` § 9).
> A veto is recorded with its reason and is visible to the operator, who may override it explicitly.
> Silent override is the failure mode; open override is a legitimate operator decision.

And the reason `compliance-lens` applies a **criteria test** rather than trusting a "no sensitive
surface here" answer: under-declaring is the common failure. People do not know their change touched
PII until someone points at the field.

---

## The lens roster — count and rationale

The brief specified ten lenses and named six. The operator supplied five domain lenses that neither
the brief nor the playbook had, then four more after a coverage review. Rather than drop brief-named
agents to hit a round number — several carry invariants the brief explicitly says not to optimize
away — **the roster is sixteen lenses plus two makers.**

| # | Lens | Class | Declares own bias? | From |
|---|---|---|---|---|
| 1 | `product-lens` | judgment | yes | operator |
| 2 | `brand-lens` | judgment | yes | operator |
| 3 | `growth-lens` | judgment | yes | operator |
| 4 | `sales-lens` | judgment | yes | operator |
| 5 | `cs-lens` | judgment | yes | coverage review |
| 6 | `ops-lens` | judgment | yes | operator (dev-ops + rev-ops) |
| 7 | `finance-lens` | judgment | yes | playbook § 8.1 |
| 8 | `analytics-lens` | judgment / adjudicator | yes | playbook § 8.1 |
| 9 | `ux-lens` | judgment | yes | brief |
| 10 | `design-lens` | judgment | yes | coverage review |
| 11 | `comms-lens` | judgment | yes | coverage review |
| 12 | `architect` | judgment | yes | brief — *pruned on non-code profiles* |
| 13 | `security-lens` | judgment | yes | coverage review — *pruned on non-code profiles* |
| 14 | `compliance-lens` | **blocking** | yes | brief |
| 15 | `customer-persona` | **reaction** | **no — by design** | brief |
| 16 | `red-team` | **adversarial** | **no — by design** | playbook § 8.4 + brief |

### Makers are not lenses — `agents/makers/`

| Maker | Produces | Judged by |
|---|---|---|
| `copywriter` | Copy for any surface | `brand-lens`, `ux-lens`, `compliance-lens` |
| `designer` | Layouts and visual specs | `design-lens`, `ux-lens`, `architect` |

**A maker never sits on the panel and never reviews its own output.** This is the fourth conflict the
integration had to resolve, and it's a category distinction rather than a collision: a panel seat
returns a *verdict*, a maker returns a *draft*, and a synthesis step has nothing to do with a draft.
Folding drafting into `brand-lens` and `ux-lens` would have been simpler and would have produced
agents grading their own work — the weakest form of review there is. Skills invoke a maker to draft,
then convene the lenses to judge what it made.

### Boundaries between adjacent seats

Several seats sit next to each other and would otherwise collapse into one voice. The splits are
documented in [`company-os/reference/lens-roster.md`](./company-os/reference/lens-roster.md) —
brand vs comms, ux vs design, ops vs cs, sales vs cs, compliance vs security, architect vs security,
analytics vs growth.

### Cost is now a first-class concern

Sixteen lenses is sixteen agents, several at `opus`/`high`. The harness therefore documents three
panel sizes — single lens, focused (a named subset, usually via `gtm` or `review-panel`), and full —
and requires any reduced run to **name the seats it dropped and the blind spot each drop creates.**
A silently reduced panel is one the operator over-trusts.

**Why every judgment lens declares its own bias.** A `finance-lens` that doesn't say *"I will be
conservative; discount me when the downside is capped"* becomes a caricature that always says no,
and the operator learns to skip it. The declared bias is what keeps an archetype useful.

**Why three lenses have no declared bias.** `customer-persona` is a gut reaction — a customer does
not caveat themselves. `red-team` is one-sided **on purpose**; soften it and the panel becomes
several flavours of agreement.

**One open seat.** The brief implies two reaction lenses; only `customer-persona` is grounded enough
to build. The second seat is deliberately left empty rather than filled with an invented archetype —
see [`company-os/reference/lens-roster.md`](./company-os/reference/lens-roster.md).

---

## What each source contributed

**Kept from the playbook, absent from the brief:** the Org North Star as the single organizing spine ·
the CoS layer (now lanes) · Teams-vs-Personal-Max scoring and cost math · the 1Password / data-
classification security model · the Phase 0–4 maturity model and Minimum Viable Rollout · the RACI ·
the dashboard build-kickoff · dual licensing.

**Kept from the brief, absent from the playbook:** read-don't-recall · evidence grading
(`SAID` < `DID` < `PAID` < `STUCK`) · per-lens declared bias · a blocking compliance lens with a
criteria test · model tiers in agent frontmatter · detect-before-asking in the interview · pruning by
deletion rather than `N/A` · routing hygiene · honest disclosure of what isn't automated · the
verification checklist.

---

## Conflict 4 — operating experience vs. the invariants above

Running the harness on a real company surfaced two places where the original design was wrong in
practice, not in theory. Both are recorded in
[`company-os/reference/operating-patterns.md`](./company-os/reference/operating-patterns.md), and both
collide directly with invariants this document declares load-bearing. Resolving them by **adding an
option and keeping the default** — rather than flipping the default — is deliberate.

### 4a. "Propose-only" made the operator the bottleneck

**The collision.** Invariant 1 says no agent acts, and merging is on the gate list. Held strictly,
every finished change waits on one person to open it and press a button. The queue stalls behind their
attention and the system's speed advantage disappears.

**Resolution.** Separate *judgment* from *mechanics*. The operator still decides; an agent may perform
the merge under a gate that can refuse. `PROFILE.md` § 6 adds `merge_authority`, **defaulting to
`operator`** — the strict reading stays the default, and `agent-on-label` is an informed opt-in with
its failure mode written down.

**The part that carries it:** agents can apply labels. The norm *an agent applies the approval label
only on the operator's explicit say-so* is what separates this from a system approving its own work
through a mechanism shaped like oversight. If that norm isn't written where agents read it, this
option is worse than the bottleneck it fixes.

### 4b. A blocking compliance lens enforces policy as law

**The collision.** Invariant 5 says `compliance-lens` can veto, and that's still right about real
exposure. But a blocking lens does not distinguish *illegal* from *our own stated policy*, and teams
write aspirational policy. Work stops on things nobody is legally or contractually required to do,
the operator starts overriding reflexively, and the signal dies exactly where it was supposed to be
strongest.

**Resolution.** `PROFILE.md` § 9 adds `compliance_posture`, **defaulting to `advisory`**. Only an
explicit written hard-line list blocks; everything else becomes `RECOMMEND-AGAINST` — recorded,
visible, argued at full strength, but not blocking. The analysis never gets softer; only its effect
changes.

**Why this default moved and the merge one didn't.** Invariant 5 originally made the veto mandatory
because under-declaring exposure is the common failure. That reasoning is still right about *detection*
— which is why the criteria test is untouched and still runs under both postures. It was wrong about
*enforcement*. A lens that blocks on self-imposed policy trains the operator to click past it, and an
override nobody reads is worse than a recommendation they argue with. The posture is now chosen from
the business: `veto` where a single exposure is existential (health, financial, children, named
regimes, enterprise security commitments), `advisory` for most early-stage software.

**The guard:** `advisory` with no hard-line list is an off switch, not a posture. The lens detects
that case, falls back to `veto`, and says so out loud — so the failure mode of skipping the question
is *more* strictness, never less.

### Why defaults didn't move

Both were learned at one company, with one operator, at one stage — `DID`-grade evidence about this
harness, not `PAID`. So both shipped as options with their failure modes stated, and **flipping a
default is the operator's decision, never an agent's**, including the agent that learned the lesson.

The operator subsequently made that call on compliance: **`advisory` by default, `veto` as the
business warrants it.** `merge_authority` still defaults to `operator`, because nobody has made the
equivalent call there and an agent should not make it by inference.

## Invariants — do not optimize these away

1. **Propose-only.** No agent acts. `PROFILE.md` § 8 is binding.
2. **Read, don't recall.** Figures come from files. The file wins.
3. **Every judgment lens declares its bias.** The three that don't are marked deliberate.
4. **`red-team` is not balanced.** That is the feature.
5. **`compliance-lens` applies its own criteria test** rather than trusting a self-declaration — under
   every posture, without exception. Whether it can *block* on what it finds is the operator's choice
   (`compliance_posture`, default `advisory`); whether it *looks* is not.
6. **Evidence grades everywhere.** Reject planning that rests on `SAID` alone.
7. **Model tiers live in agent frontmatter.** `PROFILE.md` § 10 *records* them. Never restate a tier
   inside a skill — duplicated tiers drift, and then the documentation becomes the bug. **The same
   holds for seat counts:** `reference/lens-roster.md` records how many lenses there are and which
   ones each skill convenes. A skill names its seats; it never states a count.
8. **Skills state honestly what isn't automated.** A gate described as automatic when a human has to
   run it is worse than no gate, because the operator stops checking.
9. **Prune by deletion, not `N/A`.** A stubbed section reads as configured.
10. **Routing hygiene.** Overlapping skills disambiguate in their own descriptions — see `intake`
    vs `triage`, and `agent-panel` vs `review-panel`.
11. **Build the parts that say no first.** Operating experience is blunt about this: the parts of a
    system that refuse things get used and earn trust, because when they're wrong you find out
    immediately. The parts that only produce documents have no such feedback and quietly stop being
    opened. A new skill whose entire output is a well-structured document should justify itself
    against that.
12. **Fail closed, everywhere.** An errored check is a failed check — in the merge gate, in the
    branch-deletion lookup, in a sweep that found nothing. "Scanned 0 files, all clean" must never
    read as success.
