# Gates, the sensitive surface, and what "propose-only" means

## Two different powers

Conflating these is the single most common way an operating harness goes wrong.

| Power | Who has it | What it means |
|---|---|---|
| **Act on the world** | **Nobody.** No agent, no skill, ever. | Publish, spend, merge, deploy, send externally, change the roadmap. Operator only. |
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

### Handing a gated item over

Don't just stop. Produce the thing and make it one action away:

1. **Say plainly that it's gated** and which gate.
2. **Deliver the artifact** — the drafted post, the diff, the email body, the spend request.
3. **Name what happens when it's approved** and what the reversal path is.
4. **Give the exact command** if there is one — one line, no inline `#` comments.

A gate is not a reason to deliver less. It's a reason to stop one step short.

### Approval doesn't generalize

Approval for one action covers that action. Not the next one, not the same one next week, not "things
like it." Ask again.

## The sensitive surface

Defined per-company in `PROFILE.md` § 9. When a change touches it:

- `compliance-lens` is **mandatory, not advisory** — it runs whether or not anyone asked for it.
- If it does not clear, **the verdict cannot be `ship`.** It can be `hold`, `fix-first`, or
  `escalate`. Never `ship`.
- A veto is recorded **with its reason**, in the open.
- The operator may override, explicitly. Silent override is the failure mode; open override is a
  legitimate operator decision and should be recorded as one.

### Why a criteria test, not a self-declaration

`compliance-lens` never accepts "this doesn't touch anything sensitive" as an input. It tests against
the § 9 criteria itself. **Under-declaring is the common failure** — people genuinely don't know their
change touched personal data until someone points at the field. Asking the author is asking the person
least likely to have noticed.

## Honesty about automation

**Nothing in this harness runs on a timer by itself.** Every cadence in `PROFILE.md` § 7 needs a human
to start it, or an external scheduler configured separately.

Say this whenever it's relevant. A gate the operator believes is automatic — but isn't — is worse than
no gate at all, because they stop checking for themselves.

The same applies to any check a skill claims to perform. If a skill says "verified," it must have
actually run the verification. If it couldn't, it says so and names who has to.
