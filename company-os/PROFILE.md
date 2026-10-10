# PROFILE — {{COMPANY_NAME}}

> **This file is the only place company-specific facts live.** Every skill and agent reads it; none
> hardcode a fact. Written by the `setup` skill, edited by the operator, never guessed at.
>
> **It holds pointers and stable config — never current figures.** "Runway is tracked in
> `finance/model.xlsx`" belongs here. "Runway is 14 months" does not. See § 4.
>
> Configured when `grep -c "{{" PROFILE.md` prints `0`. Sections that don't apply are **deleted**,
> not filled with `N/A` — a stub reads as configured.

| | |
|---|---|
| Configured on | {{SETUP_DATE}} |
| Configured by | {{OPERATOR_NAME}} |
| Harness version | 0.1.0 |
| Last reviewed | {{SETUP_DATE}} — review quarterly |

---

## § 1 Identity

| | |
|---|---|
| Company | {{COMPANY_NAME}} |
| One-liner | {{ONE_LINER}} |
| Stage | {{STAGE}} |
| Operator | {{OPERATOR_NAME}} — {{OPERATOR_ROLE}} |
| Team shape | {{TEAM_SHAPE}} |

**How much Claude decides alone:** {{AUTONOMY_LEVEL}}

---

## § 2 Objective function

The tie-breaker. When two options look equally good, this is what decides. Every lens weighs
recommendations against it; `red-team` attacks whether a proposal really moves it.

| | |
|---|---|
| Vision (one sentence) | {{VISION}} |
| North Star Metric | {{NSM}} |
| What the NSM proxies | {{NSM_RATIONALE}} |
| Viability constraint | {{VIABILITY_CONSTRAINT}} |
| Where the NSM is defined and measured | {{NSM_SOURCE}} |

**The objective function:** grow **{{NSM}}** *and* {{VIABILITY_CONSTRAINT}}.

> The NSM is defined once, here, and **referenced, never redefined** downstream. `analytics-lens`
> guards the definition. Its current *value* is not recorded here — read it from `{{NSM_SOURCE}}`.

---

## § 3 Customer

| | |
|---|---|
| Primary persona | {{PRIMARY_PERSONA}} |
| Secondary personas | {{SECONDARY_PERSONAS}} |
| Who actually pays | {{ECONOMIC_BUYER}} |
| Who feels the pain | {{PAIN_OWNER}} |
| The job they hire us for | {{JOB_TO_BE_DONE}} |

### Customer-quote grounding

`customer-persona` runs **only** if there is a real source of customer words. An invented composite
produces confident fiction that reads like evidence, which is worse than having no persona at all.

| | |
|---|---|
| Real quote source exists? | {{QUOTE_SOURCE_EXISTS}} |
| Where | {{QUOTE_SOURCE_PATH}} |
| `customer-persona` status | {{CUSTOMER_PERSONA_STATUS}} |

---

## § 4 Sources of truth

**This is the read-don't-recall table.** Every figure any skill or agent reports comes from a path
below. If a path here contradicts something in a prompt, in memory, or in a previous answer, **the
path wins.**

| What | Where | Grade | Owner | Instrumented? |
|---|---|---|---|---|
| Operating state / live snapshot | {{STATE_PATH}} | {{STATE_GRADE}} | {{STATE_OWNER}} | {{STATE_INSTRUMENTED}} |
| Product metrics | {{PRODUCT_METRICS_SOURCE}} | {{PRODUCT_METRICS_GRADE}} | {{PRODUCT_METRICS_OWNER}} | {{PRODUCT_METRICS_INSTRUMENTED}} |
| Financials | {{FINANCE_SOURCE}} | {{FINANCE_GRADE}} | {{FINANCE_OWNER}} | {{FINANCE_INSTRUMENTED}} |
| Pipeline / revenue | {{PIPELINE_SOURCE}} | {{PIPELINE_GRADE}} | {{PIPELINE_OWNER}} | {{PIPELINE_INSTRUMENTED}} |
| Customer evidence | {{CUSTOMER_EVIDENCE_SOURCE}} | {{CUSTOMER_EVIDENCE_GRADE}} | {{CUSTOMER_EVIDENCE_OWNER}} | {{CUSTOMER_EVIDENCE_INSTRUMENTED}} |
| Metric definitions | {{METRIC_DICTIONARY}} | — | {{METRIC_DICTIONARY_OWNER}} | — |

**Grade** is honesty about the data, not about the tool:
`A` = instrumented and trusted · `B` = mostly right, known gaps · `C` = manual, stale, or
reconstructed · `D` = someone's estimate. **Report the grade alongside any figure graded `C` or `D`.**

### Work tracking

| | |
|---|---|
| PM tool | {{PM_TOOL}} |
| Where work lives | {{PM_LOCATION}} |
| Status field + values | {{PM_STATUS_FIELD}} |
| Priority field + values | {{PM_PRIORITY_FIELD}} |
| Size / estimate field | {{PM_SIZE_FIELD}} |
| How an item names its lane | {{PM_LANE_FIELD}} |
| How to read it | {{PM_ACCESS}} |

### Tripwires — known-bad numbers

Figures that look authoritative and are wrong. Any skill that would report one of these must flag it
instead. Delete this subsection if there are none.

{{TRIPWIRES}}

---

## § 5 Lanes

A **lane** is a workstream with one owner and one NSM lever. Skills operate per-lane. A large company
has one lane per leader; a solo founder owns several. Every lane must ladder to a lever of the NSM —
a lane that doesn't is either mis-scoped or shouldn't be running.

| Lane | Owner | NSM lever | Primary lenses | Where its work lives |
|---|---|---|---|---|
{{LANES_TABLE}}

**Lane hygiene:** if two lanes claim the same lever, one of them is really a sub-lane. If a lane has
no lever, `triage` flags it every run until it's resolved or closed.

---

## § 6 Engineering

> **`setup` deletes this entire section for non-code profiles**, along with the `qa`, `pr-queue`, and
> `review-panel` skills and the `architect` agent. If you are reading this section, this profile ships
> code.

| | |
|---|---|
| Repos | {{REPOS}} |
| Stack | {{STACK}} |
| Package manager / build | {{BUILD_CMD}} |
| Test command | {{TEST_CMD}} |
| What "tested" means here | {{TEST_STANDARD}} |
| Lint / typecheck | {{LINT_CMD}} |
| Deploy command | {{DEPLOY_CMD}} |
| Deploy target(s) | {{DEPLOY_TARGET}} |
| CI | {{CI}} |
| Branch convention | {{BRANCH_CONVENTION}} |
| PR convention | {{PR_CONVENTION}} |
| Who can merge | {{MERGE_AUTHORITY}} |
| Merge posture | {{MERGE_POSTURE}} |

**Merge posture** is `operator` (default — only you merge) or `agent-on-label` (you apply an approval
label; an agent merges through a gate that refuses a stale branch, CI not green on that exact head, an
approval older than the head commit, or a failing health probe). `reference/operating-patterns.md` § 1
has the full gate and the one norm that carries it: **an agent applies the approval label only on your
explicit say-so.** Agents can apply labels, so a gate that trusts labels alone lets the system approve
itself.

**Verification standard.** Before anything is called done, exercise the **real** end-to-end path
against the real target — hit the live endpoint, run the real user flow, check the deployed bundle.
Do not infer success from a partial signal. When something fails, surface the actual error first
rather than papering over it with a retry.

---

## § 7 Cadence

| Rhythm | Interval | Owner | Skill | Automated? |
|---|---|---|---|---|
| Planning cycle | {{CYCLE_LENGTH}} | {{CYCLE_OWNER}} | `sprint-plan` | No — operator runs it |
| Cycle review | {{CYCLE_LENGTH}} | {{CYCLE_OWNER}} | `sprint-review` | No — operator runs it |
| State refresh | {{SWEEP_INTERVAL}} | {{SWEEP_OWNER}} | `state-sweep` | {{SWEEP_AUTOMATED}} |
| Blocker sweep | {{BLOCKER_INTERVAL}} | {{BLOCKER_OWNER}} | `blockers` | No — operator runs it |
| Profile review | Quarterly | {{OPERATOR_NAME}} | — | No |

**Planning style:** {{PLANNING_STYLE}}

**Where the "needs you" list goes:** {{NEEDS_YOU_DESTINATION}}

> Nothing in this harness runs on a timer by itself. Every row above needs a human to start it, or an
> external scheduler you set up separately. Stated plainly because a cadence you believe is automatic
> and isn't is worse than no cadence.

---

## § 8 Gates — what Claude may not do

**Binding.** Every skill and agent is propose-only. The items below belong to the operator, always.
Draft them, name them clearly, hand them over. Never perform them, and never treat approval of one as
approval of the next.

{{GATES_LIST}}

**One narrow exception, which is not an action:** `compliance-lens` may veto a `ship` verdict when a
change touches the sensitive surface (§ 9). It still cannot cause anything to happen. A veto is
recorded with its reason; the operator may override it explicitly and in the open.

---

## § 9 Sensitive surface

Changes touching any of these make `compliance-lens` **mandatory** — it runs and applies its own
criteria test whether or not anyone asked. Whether a non-clear finding **blocks** depends on the
posture below.

{{SENSITIVE_SURFACE}}

**`compliance-lens` applies a criteria test rather than trusting a "no sensitive surface here"
answer**, because under-declaring is the common failure — people usually don't know their change
touched personal data until someone points at the field. It does this under either posture.

**Compliance posture** is `advisory` (default — only the hard lines above block; everything else is a
recorded, visible strong recommendation) or `veto` (anything the lens doesn't clear blocks a `ship`
verdict). **Choose from the business you're actually in:** `veto` for regulated industries, health or
financial data, anything touching children, or enterprise security commitments — `advisory` for most
early-stage software, where the realistic risk is an over-eager lens teaching you to ignore it.

**The hard-line list is required either way.** An advisory posture with no hard lines is an off
switch, not a posture, so the lens detects a missing list, falls back to `veto`, and says so. Both
failure modes are in `reference/operating-patterns.md` § 2.

**Masked-output tools** are tools whose output structurally cannot carry a sensitive value — counts,
categories, pass/fail, never the record. List them above, because the distinction is invisible at the
call site and an agent will otherwise reach for the ordinary tool.

| | |
|---|---|
| Compliance posture | {{COMPLIANCE_POSTURE}} |
| Hard lines (**required** — what actually blocks) | {{COMPLIANCE_HARD_LINES}} |
| Masked-output tools | {{MASKED_OUTPUT_TOOLS}} |
| Regulatory regimes in scope | {{REGIMES}} |
| Data classes we handle | {{DATA_CLASSES}} |
| Where secrets live | {{SECRETS_MANAGER}} |
| Secret retrieval pattern | {{SECRETS_PATTERN}} |

**Non-negotiable:** credentials never enter a prompt, a commit, or shell history.

---

## § 10 Lens roster and model tiers

**Records** what each agent's frontmatter declares — it does not set it. Tiers live in agent
frontmatter and nowhere else; duplicated tiers drift, and then the documentation becomes the bug. If
this table disagrees with a `.md` file in `agents/`, **the agent file wins** and this table is stale.

| Lens | Class | Active? | Model | Effort | Declared bias |
|---|---|---|---|---|---|
| `product-lens` | judgment | {{PRODUCT_LENS_ACTIVE}} | sonnet | medium | ships too much, too early |
| `brand-lens` | judgment | {{BRAND_LENS_ACTIVE}} | sonnet | medium | protects consistency over speed |
| `growth-lens` | judgment | {{GROWTH_LENS_ACTIVE}} | sonnet | medium | over-trusts short-run numbers |
| `sales-lens` | judgment | {{SALES_LENS_ACTIVE}} | sonnet | medium | over-weights the loudest deal |
| `cs-lens` | judgment | {{CS_LENS_ACTIVE}} | sonnet | medium | protects existing customers over growth |
| `ops-lens` | judgment | {{OPS_LENS_ACTIVE}} | sonnet | medium | wants process before it's earned |
| `finance-lens` | judgment | {{FINANCE_LENS_ACTIVE}} | sonnet | medium | conservative; discounts upside |
| `analytics-lens` | adjudicator | {{ANALYTICS_LENS_ACTIVE}} | sonnet | medium | wants more data than a call needs |
| `ux-lens` | judgment | {{UX_LENS_ACTIVE}} | sonnet | medium | wants to add guidance |
| `design-lens` | judgment | {{DESIGN_LENS_ACTIVE}} | sonnet | medium | polishes before the stage earns it |
| `comms-lens` | judgment | {{COMMS_LENS_ACTIVE}} | sonnet | medium | over-prepares and delays |
| `architect` | judgment | {{ARCHITECT_ACTIVE}} | opus | high | over-generalizes too early |
| `security-lens` | judgment | {{SECURITY_LENS_ACTIVE}} | opus | high | over-weights exotic over boring |
| `compliance-lens` | **blocking** | **always** | sonnet | high | over-reads risk |
| `customer-persona` | reaction | {{CUSTOMER_PERSONA_STATUS}} | sonnet | medium | **none — deliberate** |
| `red-team` | adversarial | **always** | sonnet | high | **none — one-sided on purpose** |

**Makers — `agents/makers/`. Never sit on the panel.**

| Maker | Produces | Model | Effort | Reviewed by |
|---|---|---|---|---|
| `copywriter` | Copy for any surface | sonnet | medium | `brand-lens`, `ux-lens`, `compliance-lens` |
| `designer` | Layouts and visual specs | sonnet | medium | `design-lens`, `ux-lens`, `architect` |

A maker never reviews its own output. Separating make from judge is what makes the review mean
anything.

**Why the two at the bottom of the lens table declare no bias.** A customer doesn't caveat themselves,
and `red-team` is meant to be one-sided — soften it and the panel becomes several flavours of agreement.

**Why the others do.** A `finance-lens` that never says *"I'm being conservative here; discount me
when the downside is capped"* becomes a caricature that always says no, and you learn to skip it.

**Cost — this matters.** A full `/agent-panel` is **sixteen separate agents**, some at `opus`/`high`.
Reserve it for hard-to-reverse decisions. For everything else call one lens, or use a focused skill
(`gtm`, `review-panel`) that convenes a named subset. Dropping seats is expected, not a compromise — but
any reduced run must say which seats it skipped and what blind spot that creates.

To change a tier, edit the **agent frontmatter** and then update this table to match. Never the reverse.

---

## § 11 Open questions

Recorded during setup as `TBD — open question` because the honest answer was "I don't know."
**Reported to the operator, never silently left here.** Each one degrades some skill; the degradation
is named so you know what you're missing.

{{OPEN_QUESTIONS}}
