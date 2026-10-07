# AI-Native Startup OS

**A practical, executable playbook for rolling out Claude across a startup — plus a runnable Claude
Code harness that operates it.**

> The whole company gets faster *without* fragmenting its knowledge. Individual AI output becomes
> governed, reusable, discoverable institutional memory — and every agent, dashboard, and decision points
> at one **Org North Star**.

📖 **Read the story:** [The AI-Native Startup Operating System](https://www.michaeltaus.com/ai-native-startup-os/)
📄 **The playbook:** [`ai-native-startup-os.md`](./ai-native-startup-os.md) — the operating model
⚙️ **The harness:** [`company-os/`](./company-os/) — a Claude Code plugin that runs it

Two halves. The playbook is the **substrate** — what a company has to decide. The harness is the
**runtime** — what Claude Code executes against those decisions. Roughly 80% of the value is in the
substrate; the harness is the 20% that makes it run.
[`RECONCILIATION.md`](./RECONCILIATION.md) explains how they fit together and where they disagreed.

---

## Install the harness

From any Claude Code session, in the profile where you want it:

```bash
/plugin marketplace add miketaus/ai-native-startup-os
```

```bash
/plugin install company-os
```

Then run the interview:

```bash
/company-os:setup
```

**No git and no plugin system?** Copy the directory instead — `company-os/CLAUDE.md` makes it load
automatically:

```bash
cp -R company-os <target-profile-dir>
```

Either way, `setup` interviews you and writes `PROFILE.md`. Nothing else runs until it does.

### One profile at a time

Configure each company or profile separately. `PROFILE.md` is what keeps one company's context out of
another's — never symlink a single copy between profiles, and never merge these skills into a global
`~/.claude/skills/`.

---

## What happens when you run `/setup`

It ships with **no company data** and configures itself by interviewing you. Six phases:

| Phase | Asks about |
|---|---|
| **0 — recon** | *Nothing.* Detects repo, remote, stack from manifests, CI, deploy config, existing docs, `gh` auth, board |
| **1 — identity** | Company, stage, **objective function** (the tie-breaker), personas, who pays, whether real customer quotes exist |
| **2 — sources of truth** | Where operating state lives, what's actually instrumented vs wished for, financial source **and its honest grade**, PM tool and exact field names, any known-bad number worth a tripwire |
| **3 — engineering** | Repos, stack, deploy, tests, branch convention — **skipped entirely for non-code profiles** |
| **4 — lanes, cadence, gates** | Workstreams and their levers, planning rhythm, where the "needs you" list goes, the gate list, the sensitive surface |
| **5 — lenses** | Which of the sixteen lenses are active, and at what model tier |

It **detects before asking**, **batches questions** rather than interrogating you one at a time,
always offers a recommended default, and when you say "I don't know" it records `TBD — open question`
instead of inventing an answer.

Then it **prunes by deletion** — no code means the engineering skills, the `architect` and
`security-lens` agents, and `PROFILE.md` § 6 are removed, not stubbed. A stubbed section reads as
configured.

---

## `/agent-panel` — sixteen lenses

The headline skill. Convenes every active lens on a decision, in parallel and independently, then
reports **where they disagreed** — because that's where the decision actually lives.

| | Lenses |
|---|---|
| **Product & customer** | `product-lens` · `ux-lens` · `design-lens` · `customer-persona` |
| **Commercial** | `brand-lens` · `growth-lens` · `sales-lens` · `cs-lens` · `comms-lens` |
| **Operating & money** | `ops-lens` (dev-ops + rev-ops) · `finance-lens` · `analytics-lens` |
| **Technical & risk** | `architect` · `security-lens` · `compliance-lens` |
| **Adversarial** | `red-team` |

Plus a separate **maker** class — `copywriter` and `designer` — that produces drafts for the lenses to
judge. **Makers never sit on the panel and never review their own output.**

**If every lens agrees, that's a finding, not a confirmation.** A panel that always reaches consensus
has stopped working.

A full sixteen-seat run is a real spend, so it's meant for one-way doors. Most questions want a single
lens, or a focused skill that convenes a named subset of the roster.

## The skills

| Skill | Does |
|---|---|
| `setup` | The interview. Writes `PROFILE.md`, prunes, verifies, stops |
| `agent-panel` | Full lens panel on a consequential decision |
| `intake` | Should this new thing become work at all? |
| `triage` | Of everything already open, what's next? |
| `blockers` | What's stuck, and the "needs you" list |
| `state-sweep` | Re-read every source; draft an updated snapshot; flag contradictions |
| `sprint-plan` · `sprint-review` | Commit a cycle; close it honestly *(dropped if you plan continuously)* |
| `gtm` | Positioning, launch, campaign, pricing — the commercial seats |
| `review-panel` · `qa` · `pr-queue` | Review a change; verify the real path; report the queue *(code profiles only)* |

---

## The invariants

The parts that look like style choices and aren't:

- **Propose-only.** No agent acts. Publishing, spend, merges, deploys, roadmap changes are yours.
- **Figures: read, don't recall.** Nothing states a current number from memory. Every figure comes from
  a path in `PROFILE.md` § 4, with a source grade. **If a file contradicts the prompt, the file wins.**
- **Evidence grades: `SAID` < `DID` < `PAID` < `STUCK`.** Stated with the claim. Never plan on `SAID`
  alone.
- **Every judgment lens declares its own bias.** `finance-lens` says it will be conservative;
  `ux-lens` says it wants to add guidance. That self-check is what stops an archetype becoming a
  caricature you learn to skip.
- **`red-team` is not balanced, on purpose.** Soften it and the panel becomes several flavours of
  agreement.
- **`compliance-lens` always runs** on anything touching the sensitive surface, and always applies
  its own criteria test rather than trusting a "nothing sensitive here" answer. Whether it can
  **block** a `ship` verdict is a per-company choice (`compliance_posture`: `advisory` by default,
  `veto` where the business warrants it).
- **Skills state what isn't automated.** Nothing here runs on a timer. A gate you believe is automatic
  and isn't is worse than no gate.

## What's deliberately blank

The hard-won specifics: the structural truths each lens should know cold, the recurring failure
patterns `red-team` attacks with, real customer quotes, and known-bad numbers worth a tripwire.

These can't be invented. A lens without them gives generic advice in a confident voice — the exact
failure this harness exists to prevent. `setup` asks for them, but expect the genuinely useful version
to take a few cycles of real operating data. Budget accordingly.

---

## What's inside the playbook

- **The operating model** — personal Claude accounts as the execution layer, with a system of record
  (GitHub), a secrets manager (1Password), and an identity provider (Google Workspace) around them.
- **Teams vs. Personal Max** — a scored decision framework + the cost math.
- **The Org North Star** — one product metric, decomposed into AARRR, wired into the dashboard, the
  agents, and the red-team's challenge test.
- **A cascading rollout** — exec onboarding → generated per-user onboarding → dashboard kickoff → user
  rollout.
- **A Phase 0–4 maturity model + a Minimum Viable Rollout** so you don't need the whole architecture
  on day one.
- **The honest trade-offs** — data handling, account ownership, vendor coupling.

**Quick start without the harness:** the **Minimum Viable Rollout** (§13.0) — one repo, one north
star, one metric dictionary, one Chief-of-Staff agent, one onboarding file.

## License

Dual-licensed so the prose and the code each get the right terms:

- **Documentation / prose** — [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **Code, configs & templates** — [MIT](./LICENSE)

When you reuse or adapt it, keep the credit:

> *"AI-Native Startup OS — Master Runbook & Playbook"* by **Michael Taus** with **Claude (Anthropic)** —
> licensed CC BY 4.0 (docs) / MIT (code). Source: https://www.michaeltaus.com/ai-native-startup-os/

## Author

**Michael Taus** — [michaeltaus.com](https://www.michaeltaus.com/) ·
[LinkedIn](https://www.linkedin.com/in/mtaus/). Co-authored with Claude (Anthropic).

*If you build on it, I'd genuinely love to hear what you change.*
