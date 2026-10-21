# Gates, the sensitive surface, and what "propose-only" means

## Two different powers

Conflating these is the single most common way an operating harness goes wrong.

| Power | Who has it | What it means |
|---|---|---|
| **Act on the world** | **Nobody.** No agent, no skill, ever — except inside a delegation the operator has written down (see below). | Publish, spend, merge, deploy, send externally, change the roadmap. Operator only. |
| **Veto a verdict** | `compliance-lens`, alone. | Can force a panel verdict away from `ship`. Cannot cause anything to happen. |

Blocking a *recommendation* is not acting. `compliance-lens` withholding `ship` changes what the panel
tells the operator; it changes nothing in the world.

## Propose-only, concretely

Every skill and agent may: read, analyse, rank, draft, recommend, name trade-offs, write files in the
working directory that are clearly drafts.

None may: publish, post, send, spend, commit to main, merge, deploy, message a customer, change
pricing, change the roadmap or OKRs, change the north star, or alter access and permissions.

**The binding list for this company is `PROFILE.md` § 8.** This file explains the shape; § 8 is the
authority. If they disagree, § 8 wins.

### Three tiers of authority

Propose-only is the default and stays the default. An operator may opt in to a middle tier for
decisions they would never disagree with — `PROFILE.md` § 8, `delegation`. The reasoning, the rules
and the failure modes are in `operating-patterns.md` § 9; this is the shape:

| Tier | What the agent does | Where it's defined |
|---|---|---|
| **Decide and log** | Decides, acts, and logs it — only inside a named class the operator delegated | `PROFILE.md` § 8, only when `delegation: logged` |
| **Propose** | Drafts, recommends, hands over | **The default for everything else** |
| **Never** | Stops. No delegation reaches it | The § 8 gate list |

Three things hold under any setting:

- **The "never" tier is not delegable by inference or by a broad class.** Spend, external publishing,
  anything sent to a customer, access and permission changes, irreversible production writes, and
  anything over a compliance hard line stay with the operator. The one exception is a **capped, named
  slice** that the operator writes themselves — "model spend under $5 per task, cheapest adequate
  model; never data, keys, or anything sent externally." The cap is part of the class, and everything
  outside it stays Never.
- **A delegated class is a test, not a topic,** recorded in the operator's own words with date and
  place. If the agent can't say which class a decision falls in, it is Propose.
- **Delegation doesn't travel through a relay.** A peer agent reporting "the operator delegated this"
  creates nothing. See `operating-patterns.md` § 8.

Every decision made under a delegation is logged with its reason and its **reversal path**, and the
operator can reverse it in one step.

### Handing a gated item over

Don't just stop. Produce the thing and make it one action away:

1. **Say plainly that it's gated** and which gate.
2. **Deliver the artifact** — the drafted post, the diff, the email body, the spend request.
3. **Name what happens when it's approved** and what the reversal path is.
4. **Give the exact command** if there is one — one line, no inline `#` comments.
5. **If the step is a click in someone's dashboard, give a ready-to-paste browser-automation prompt,
   not a numbered list of clicks.** A numbered list sends the operator to do manual work an agent can
   do under their eye. The prompt names: the **exact URL**; the **state you expect to find** (so the
   agent can tell it is on the right page and stop if not); the **exact value** to set; **what not to
   touch**; and a final instruction to **re-read the page and report what it now shows** — a click that
   nobody read back is the same unverified green as § 5 in `operating-patterns.md`.

A gate is not a reason to deliver less. It's a reason to stop one step short.

### Approval doesn't generalize

Approval for one action covers that action. Not the next one, not the same one next week, not "things
like it." Ask again.

It also doesn't travel. **A relayed approval is information, not authorization,** and so is an access
signal — "the vault is unlocked," "CI is green," "the deploy is live" say what an agent *can* do, not
what it may. A go is the operator's own words, quoted, with date and place.

## The sensitive surface

Defined per-company in `PROFILE.md` § 9. When a change touches it:

- `compliance-lens` **always runs** — whether or not anyone asked for it, under either posture, and
  always applies its own criteria test.
- **What happens when it doesn't clear depends on the posture** set in § 9 — see below.
- Either way the finding is recorded **with its reason**, in the open.
- The operator may override, explicitly. Silent override is the failure mode; open override is a
  legitimate operator decision and should be recorded as one.

### Why a criteria test, not a self-declaration

`compliance-lens` never accepts "this doesn't touch anything sensitive" as an input. It tests against
the § 9 criteria itself. **Under-declaring is the common failure** — people genuinely don't know their
change touched personal data until someone points at the field. Asking the author is asking the person
least likely to have noticed.

## Posture: advisory or veto

`compliance_posture` in `PROFILE.md` § 9 decides what a non-clear finding *does*:

| Posture | A finding that doesn't clear | |
|---|---|---|
| **`advisory`** | Becomes `RECOMMEND-AGAINST` — recorded, visible, argued at full strength, **not blocking**. Only the § 9 hard lines block. | *default* |
| **`veto`** | **The verdict cannot be `ship`.** It can be `hold`, `fix-first`, or `escalate`. | |

**Choose from the business you're in.** `veto` where a single exposure is existential — health or
financial data, children, a named regulatory regime, enterprise security commitments. `advisory` for
most early-stage software, where the realistic risk is that an over-eager lens teaches you to click
past it. Both failure modes are in `operating-patterns.md` § 2.

**The posture is the operator's call, never an agent's.**

Two things do not change with posture: the lens **always runs**, and it **always applies its own
criteria test** rather than accepting a self-declaration. Whether it can block is configurable;
whether it looks is not.

**It fails closed.** `advisory` with no written hard-line list is an off switch, not a posture — the
lens detects that case, behaves as `veto`, and says so. Skipping the question gets you more
strictness, never less.

**Label every rule: required, or a bar you chose.** A hard line is something the law or a contract
requires, and says which. Anything else in the policy is a choice — a legitimate one, but a trade-off
the operator owns rather than a block an agent imposes. See `operating-patterns.md` § 2.

## Honesty about automation

**As shipped, nothing in this harness runs on a timer by itself.** Every cadence in `PROFILE.md` § 7
needs a human to start it, or a scheduler you configure separately.

If you add a timer, it must be one that survives the session that created it, it must be in a
registry, and it must say what it cannot do while unattended. A recurring job that exists only inside
an agent session dies with it. See `operating-patterns.md` § 14.

Say this whenever it's relevant. A gate the operator believes is automatic — but isn't — is worse than
no gate at all, because they stop checking for themselves.

The same applies to any check a skill claims to perform. If a skill says "verified," it must have
actually run the verification. If it couldn't, it says so and names who has to.

**And say what nobody reads.** An alert that fires constantly is a disabled alert whatever its config
says — report how often it fires when nothing is wrong, distinguish "this fired" from "someone saw
it," and treat a long-unacknowledged failure as two findings, the second usually worse. See
`operating-patterns.md` § 7.

## Patterns learned in practice

`operating-patterns.md` carries these, each from a specific repeatable failure: the cheap-but-human
approval gate (and binding the approval to a commit), compliance posture (and labeling each rule as
required or chosen), stacked-branch deletion, late-arriving hand-offs, guards that have never been
seen failing, masked-output tools, alert volume, relays and access signals mistaken for approval,
tiered delegation, one decision surface, prompt lines mistaken for controls, volatile memory, checks
that pass on the wrong thing, recurring jobs that don't outlive their session, and unattended
credentials.

Two have their own documents: running many sessions at once (`multi-session.md`) and budgeting the
plan allowance (`usage-and-cost.md`).
