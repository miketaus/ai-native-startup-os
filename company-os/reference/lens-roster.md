# The lens roster

**Sixteen lenses. Together they are `/agent-panel`.** Individually they can be called on their own
when a question only needs one seat — which is most of the time.

Plus a separate **maker** class that produces work rather than judging it. Makers never sit on the
panel.

## The lenses

| # | Lens | Owns the question | Class | Declares bias |
|---|---|---|---|---|
| 1 | `product-lens` | Is this the right thing to build, now, at this scope? | judgment | yes |
| 2 | `brand-lens` | Does this sound like us, and does it compound or spend reputation? | judgment | yes |
| 3 | `growth-lens` | Will this acquire, activate, or retain — and does the channel scale? | judgment | yes |
| 4 | `sales-lens` | Can this be sold, by whom, to whom, against what objection? | judgment | yes |
| 5 | `cs-lens` | Will customers succeed after they buy, and will they stay? | judgment | yes |
| 6 | `ops-lens` | Who runs this on day 2, and what breaks at 10×? (dev-ops **and** rev-ops) | judgment | yes |
| 7 | `finance-lens` | What does it cost, what does it return, what does it do to runway? | judgment | yes |
| 8 | `analytics-lens` | Is this measurable, is the metric honest, is the number real? | adjudicator | yes |
| 9 | `ux-lens` | Can a real person accomplish this without being told how? | judgment | yes |
| 10 | `design-lens` | Does this communicate before it's read, and look deliberate? | judgment | yes |
| 11 | `comms-lens` | Who hears what, when, and in what order? | judgment | yes |
| 12 | `architect` | Does this fit the system we have, and what does it cost structurally? | judgment | yes |
| 13 | `security-lens` | Could an attacker make this do something we didn't intend? | judgment | yes |
| 14 | `compliance-lens` | Does this create legal, regulatory, or data exposure? | **can block** | yes |
| 15 | `customer-persona` | *Reacts as the customer.* Not analysis — reaction. | **reaction** | **no** |
| 16 | `red-team` | What's the strongest case that this is wrong? | **adversarial** | **no** |

`architect` and `security-lens` are deleted by `setup` on profiles that ship no code.

## The makers — `agents/makers/`

| Maker | Produces | Reviewed by |
|---|---|---|
| `copywriter` | Landing pages, emails, in-product strings, announcements, ad variants | `brand-lens`, `ux-lens`, `compliance-lens` |
| `designer` | Layouts, component structures, visual specs, slide structures | `design-lens`, `ux-lens`, `architect` |

**Makers never sit on the panel, and never review their own output.** An agent grading its own work is
the weakest form of review there is, and the separation is the entire reason the review means anything.
Skills invoke a maker to *draft*, then send the draft to the relevant lenses to *judge*.

## Adjacent pairs — who owns what

Several seats sit next to each other. The boundaries, so seats don't collapse into each other:

| Pair | The split |
|---|---|
| `brand-lens` / `comms-lens` | Brand: **how we sound**. Comms: **who hears it, when, in what order**. |
| `brand-lens` / `copywriter` | Brand **judges** the words. Copywriter **writes** them. |
| `ux-lens` / `design-lens` | UX: **can they complete the task**. Design: **does it communicate and look deliberate**. |
| `design-lens` / `designer` | Design-lens **judges** the layout. Designer **produces** it. |
| `ops-lens` / `cs-lens` | Ops: **our** day-2 load and internal handoffs. CS: **the customer's** day-2 experience and whether they stay. |
| `sales-lens` / `cs-lens` | Sales: can we **close** it. CS: can they **succeed** with it afterwards. |
| `compliance-lens` / `security-lens` | Compliance: **are we permitted**. Security: **could an attacker**. A change can pass one and fail the other. |
| `architect` / `security-lens` | Architect: does it **fit and hold up**. Security: can it be **made to misbehave**. |
| `analytics-lens` / `growth-lens` | Analytics: is the number **honest**. Growth: is the number **worth chasing**. |

## The classes

**Judgment lenses (1–13)** analyse from a domain seat and **declare their own bias**. That declaration
is what stops an archetype becoming a caricature. A `finance-lens` that never says *"I'm being
conservative; discount me when the downside is capped"* becomes the agent that always says no, and you
learn to skip it. Each is entitled to be wrong in its stated direction — that's why the panel has more
than one seat.

**The blocking lens (14)** is the only agent whose finding can change a verdict on its own. It
**always runs** for anything touching the sensitive surface and always applies its own criteria test.
Whether a non-clear finding actually blocks depends on `compliance_posture` in `PROFILE.md` § 9 —
`advisory` by default, `veto` where the business warrants it. See `gates.md`.

**Bias-free by design (15–16).** `customer-persona` is a **reaction**, not an analysis — a customer
doesn't caveat themselves, and making it balanced would destroy the thing it's for. `red-team` is
**one-sided on purpose**; soften it and the panel becomes several flavours of agreement.

## `customer-persona` refuses to run ungrounded

If `PROFILE.md` § 3 names no real quote source, `customer-persona` **declines and says why.** It does
not improvise a composite. An invented customer produces confident fiction that reads like evidence —
strictly worse than no persona, because the operator can't tell the difference.

To activate it: point § 3 at real customer words — interview transcripts, support tickets, sales-call
notes, churn surveys, review sites. Then rerun `setup` or edit § 3 directly.

## The open reaction seat

The design calls for **two** reaction lenses. Only `customer-persona` has a grounding source concrete
enough to build. The second seat is **deliberately empty** rather than filled with an invented
archetype — a plausible-sounding "investor reaction" with nothing behind it is exactly the
confident-fiction failure this harness exists to prevent.

Fill it when a second population is both **decision-relevant** and **documented somewhere real**.
Candidates that sometimes qualify: an investor or board member (grounded in actual board minutes and
diligence questions), a frontline support rep (grounded in ticket queues), a partner or reseller
(grounded in partner calls).

## Which seats run when

`/agent-panel` runs every **active** lens (`PROFILE.md` § 10), plus `compliance-lens` and `red-team`
always. Other skills convene a subset — each names its own:

| Skill | Convenes |
|---|---|
| `agent-panel` | all active + `compliance-lens` + `red-team` |
| `review-panel` | `architect`, `security-lens`, `ops-lens`, `ux-lens` (only if user-facing), `compliance-lens` (**mandatory**), `red-team` |
| `gtm` | `brand-lens`, `growth-lens`, `sales-lens`, `comms-lens`, `customer-persona` (only if § 3 is grounded), `finance-lens`, `red-team` — plus `compliance-lens` whenever the work makes a public claim |
| `sprint-plan` | `product-lens`, `analytics-lens`, `finance-lens`, `ops-lens`, `red-team` |
| `sprint-review` | `analytics-lens`, `product-lens`, `finance-lens`, `cs-lens` |
| `intake` | `product-lens`, `analytics-lens` (+ `customer-persona` if grounded) |
| `triage` / `blockers` / `qa` / `state-sweep` | none — these rank, verify, and report; they don't convene |
| `pr-queue` | none — but it flags any PR touching § 9 as needing `compliance-lens` before merge |
| `setup` | none — it configures the roster rather than convening it |

**This table is where seat counts live.** A skill names the seats it convenes; the count of them
belongs here and nowhere else. Never write "six seats" or "sixteen lenses" into a skill file —
duplicated counts drift, and then the documentation becomes the bug. If a skill's convene list and
this table disagree, **the skill file wins** on *which* lenses and this row is stale.

## Cost — read this before running a full panel

Sixteen lenses is **sixteen separate agents**, several at `opus`/`high`. A full `/agent-panel` is a
serious spend and should be reserved for genuinely consequential, hard-to-reverse decisions.

Three sensible sizes:

| Size | Seats | Use for |
|---|---|---|
| **Single lens** | 1 | Most questions. Just call the seat you need. |
| **Focused panel** | 4–7 | A real decision inside one domain. Use `gtm`, `review-panel`, or name the seats. |
| **Full panel** | all active | Pivots, pricing, launches, one-way doors, "we're sure about this" moments. |

Dropping seats is expected, not a compromise. Any skill that runs a reduced panel **must say which
seats it skipped and what blind spot each drop creates** — a silently reduced panel is one the operator
over-trusts.

Tiers are changed in agent frontmatter and recorded in `PROFILE.md` § 10 — **never in a skill file.**
Duplicated tiers drift, and then the documentation becomes the bug.

## Disagreement is the product

**If every lens agrees, something is wrong.** Either the question was too easy to be worth a panel, the
lenses have collapsed into one voice, or they're all reading the same thin evidence. A panel that
always reaches consensus has stopped working — say so rather than presenting false agreement as
confidence.

The synthesis reports **where the lenses split and why**, not just the majority.
