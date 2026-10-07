---
name: setup
description: Configure the company operating harness by interviewing the operator, then write PROFILE.md and prune what doesn't apply. Run this FIRST, before any other company-os skill, whenever PROFILE.md still contains {{PLACEHOLDER}} markers. Also use to reconfigure after a material change — new stage, new PM tool, new lanes.
---

# setup — configure the harness

You are configuring a blank harness for one company. When you finish, `PROFILE.md` holds this
company's real operating context and every other skill reads it.

## Hard rules

1. **Detect before asking.** Run Phase 0 first. Only ask about genuine gaps. Nothing annoys an
   operator faster than being asked what's plainly visible in their repo.
2. **Batch questions.** Ask a phase's worth at a time, numbered, with a recommended default on each.
   Never interrogate one question at a time.
3. **Always offer a default** and say why you're recommending it, so "yes" is a real answer.
4. **Never invent.** If the operator says "I don't know," write `TBD — open question` and move on.
   Record it for § 11. A guessed fact is the failure this whole harness exists to prevent.
5. **Never write a current figure into `PROFILE.md`.** This is **figures: read, don't recall** in its
   setup-specific form — record *where* a figure lives and its grade, never the figure. "Runway is
   tracked in `finance/model.xlsx`, grade B" — never "runway is 14 months." A profile that names
   figures will argue confidently from stale numbers within a quarter, and every skill downstream
   inherits the error. When Phase 0 detection contradicts what the operator tells you, say so and
   let them resolve it — **don't silently pick one.**
6. **Prune by deletion, not `N/A`.** A stubbed section reads as configured.
7. **Stop when done.** Do not run another skill. Report, and let the operator start.

## Your own boundary

`setup` is the one skill that writes rather than proposes — that's what it's for. It is still bounded:

- **You write `PROFILE.md`, scaffold files the operator agreed to, and prune the harness.** Nothing else.
- **Ask before modifying an existing `CLAUDE.md`**, and show the diff first. Never overwrite one.
- **Never commit, push, open a PR, or change anything outside the profile directory.**
- **Everything in the § 8 gate list you just wrote applies to you too**, from the moment you write it.
- **Never invent a fact to fill a placeholder.** `TBD — open question` is always the better answer.

---

## Phase 0 — recon (ask nothing)

Work silently. Then tell the operator what you found so they only correct it.

- Is this a git repo? Remote? Default branch? Recent commit cadence and contributors.
- Stack, from manifests: `package.json`, `pyproject.toml`, `go.mod`, `Gemfile`, `Cargo.toml`,
  `*.csproj`, `composer.json`.
- Scripts that reveal build/test/deploy/lint commands.
- CI: `.github/workflows/`, `.gitlab-ci.yml`, `.circleci/`.
- Existing docs: `README`, `CONTRIBUTING`, `docs/`, `ADR`s, any `CLAUDE.md`.
- Deploy config: `vercel.json`, `wrangler.*`, `fly.toml`, `Dockerfile`, `render.yaml`, `netlify.toml`.
- Is `gh` authenticated? Is there an issue tracker or project board in use?
- Any existing `north-star.md`, `metrics/`, `STATE.md`, `tooling.md` from the playbook's `org-os` layout.

**Then decide the single most consequential thing:** does this profile ship code?

Open with a summary and the code question:

> Before I ask anything, here's what I can see: **[findings]**.
> Correct me where I'm wrong. One thing I can't detect: **does this profile ship code?** If yes I'll
> keep the engineering skills and the `architect` lens; if no I'll delete them rather than leave them
> as dead weight.

---

## Phase 1 — identity and objective function

Batch these:

1. **Company name** and a **one-line description** — what it does, for whom.
2. **Stage** — pre-product / pre-revenue / early revenue / scaling / profitable.
3. **Team shape** — solo, founders only, or headcount by function.
4. **The objective function.** *"When two options look equally good, what decides?"* Push for one
   metric plus a survival constraint: *"grow weekly active teams **and** move toward break-even."*
   This is the tie-breaker every lens uses — spend real time here.
   - If they name a vanity metric (signups, logins, page views), say so and ask what it would mean for
     a customer to actually get value. **Recommend, don't overrule.**
   - If they name revenue alone, note it's lagging and ask what leads it.
5. **Personas** — who uses it, who feels the pain, **who actually pays** (often different people).
6. **The job** they hire you for.
7. **Customer quotes** — *"Do you have a real source of customer words? Transcripts, support tickets,
   sales-call notes, churn surveys, review sites?"*
   - **If no:** say plainly that `customer-persona` will stay inactive, and why — an invented composite
     produces confident fiction that reads like evidence, which is worse than no persona because
     nobody can tell the difference. Don't negotiate this.
8. **How much should Claude decide alone?** Offer three: *propose everything* (default, recommended) ·
   *decide reversible things, propose the rest* · *decide within a lane, propose across lanes*.

---

## Phase 2 — sources of truth

The § 4 table is the most load-bearing thing you'll write. Every figure any skill reports comes from
it. Batch:

1. **Where operating state lives** — the live "what's true right now" snapshot. If nowhere, offer to
   scaffold `source-of-truth/STATE.md`.
2. **Product metrics** — tool and, critically, **what's actually instrumented versus wished for.**
   Ask directly: *"If I asked for activation rate today, could you get it, or would someone have to
   build it first?"*
3. **Financial source** — spreadsheet, accounting tool, a model — **and its honest grade**:
   `A` instrumented and trusted · `B` mostly right, known gaps · `C` manual, stale, or reconstructed ·
   `D` someone's estimate. Most early companies are `C`. Say that's fine and normal; what matters is
   that it's labelled, so `finance-lens` reports the grade with the figure.
4. **Pipeline / revenue source** and its grade.
5. **Customer evidence source** and its grade (this is what grounds `customer-persona`).
6. **Metric definitions** — is there a dictionary? If not, offer to scaffold `metrics/aarrr.md`.
7. **PM tool + exact field names and values.** Not "we use Linear" — *which* status values, *which*
   priority scale, how an item names its lane. `triage` and `blockers` are useless without these.
8. **Tripwires** — *"Is there a number in your systems that looks authoritative and is wrong?"*
   Stale MRR in a CRM, a double-counting dashboard, a funnel with a broken step. Operators almost
   always have one. Recording it prevents a whole class of confident errors.

---

## Phase 3 — engineering

**Skip this phase entirely if Phase 0 established there's no code.** Don't ask, don't mention it.

Otherwise confirm what you detected and fill gaps: repos · stack · build · **test command and what
"tested" actually means here** · lint/typecheck · deploy command and target · CI · branch and PR
convention · who can merge.

Ask one thing you can't detect: *"When something is called done, what's the real check — tests
passing, or someone exercising the live path?"* Record the honest answer in `TEST_STANDARD`.

**Then ask the merge posture** (`MERGE_POSTURE`). Offer both, recommend `operator` to start:

> *"Who presses merge? Either **you do** — safest, but you become the bottleneck and the queue
> stalls behind your attention. Or **you approve with a label and an agent merges** through a gate
> that refuses a stale branch, CI that isn't green on that exact commit, an approval older than the
> code it approves, or a failing health probe."*

If they pick `agent-on-label`, say this plainly and record that you did: **agents can apply labels.**
The norm "an agent applies the approval label only on your explicit say-so" is what makes this a gate
rather than a system approving its own work. `reference/operating-patterns.md` § 1 has the full gate.

---

## Phase 4 — lanes, cadence, and gates

1. **Lanes.** The workstreams. For each: name, owner, and **which NSM lever it moves**. If a lane has
   no lever, flag it — it's mis-scoped or shouldn't be running. Three to seven is typical; a solo
   founder owns several.
2. **Planning style** — cyclical (sprints/cycles) or continuous.
   - **If continuous:** offer to delete `sprint-plan` and `sprint-review`. Recommend deleting —
     unused skills clutter routing and make the harness look like it does things it doesn't.
3. **Cycle length** and **who owns the cycle**, if cyclical.
4. **Sweep intervals** for `state-sweep` and `blockers`, and their owners.
5. **Where the "needs you" list goes** — a file, a channel, a board view, or straight into the session.
6. **The gate list.** Start from this default and let them add:
   > publish or post anything externally · spend money · merge to the default branch · deploy to
   > production · send anything to a customer · change pricing · change the roadmap or OKRs · change
   > the north star · change access or permissions · sign or agree to anything
7. **The sensitive surface** — what makes `compliance-lens` mandatory. Regimes in scope, data classes
   handled, where secrets live, the retrieval pattern.
   - **Push back on "nothing's sensitive."** Ask specifically: do you store customer emails? free-text
     fields? payment data? Does anything leave your systems to a third party? Under-declaring here is
     the common failure, which is exactly why `compliance-lens` applies its own criteria test rather
     than trusting this answer.
8. **Compliance posture** (`COMPLIANCE_POSTURE`). Recommend `veto` to start:
   > *"Should the compliance lens be able to **block**, or only **advise**? Blocking is safer, but it
   > will enforce your own internal policies as though they were law, and you'll start overriding it
   > reflexively — which kills the signal for the cases that matter. Advisory means only an explicit
   > list of hard lines blocks."*
   - **If they choose `advisory`, you must get the hard-line list** (`COMPLIANCE_HARD_LINES`) in the
     same breath. Only things actually illegal or contractually binding — not things merely unwise.
     An advisory posture with no hard lines is an off switch, and the lens is built to treat it as
     `veto` and say so. If they can't name any, record `TBD — open question` and default to `veto`.
9. **Masked-output tools** (`MASKED_OUTPUT_TOOLS`). Ask whether any review has to happen without the
   underlying data entering an agent's context at all. If so, note which tools structurally cannot
   emit the sensitive value — counts and pass/fail rather than records. Delete the row if none.

---

## Phase 5 — lenses and tiers

Show the roster from `reference/lens-roster.md` with defaults already applied, and ask what to
**change** — don't ask a separate question per lens.

- **Always on, not optional:** `compliance-lens` (it can veto), `red-team` (it's the only reason the
  panel isn't an echo chamber).
- **On by default:** `product-lens`, `finance-lens`, `analytics-lens`, `ops-lens`, `ux-lens`, plus
  `brand-lens`, `growth-lens`, `sales-lens`, `cs-lens`, `comms-lens` for any company with customers.
- **`design-lens`:** on if the profile has any visual surface — product UI, marketing site, decks.
- **`architect` and `security-lens`:** on for code profiles, **deleted** otherwise.
- **`customer-persona`:** on **only** if Phase 1 found a real quote source. Otherwise inactive, stated
  plainly, with the one-line path to activating it later.
- **Makers** (`copywriter`, `designer`) are always available and are **never panel seats.** Mention
  they exist and that skills invoke them to draft, then lenses judge the draft.

Then flag cost honestly — this matters more the larger the roster is:

> A full `/agent-panel` is one separate agent per active lens, several on the largest model. That's a
> real spend, and it's meant for one-way doors — pivots, pricing, launches. Most questions want one
> lens, or a focused skill like `gtm` or `review-panel` that convenes a named subset.
>
> You can turn lenses off now, or downgrade tiers. My recommendation: leave them alone until you've
> run it a few times and know which seats you actually read. Turning off a seat you've never used is
> guessing in the other direction.

---

## Then write

1. **Write `PROFILE.md`.** Every `{{PLACEHOLDER}}` replaced. Delete inapplicable sections entirely —
   § 6 for non-code profiles, the tripwires subsection if there are none, § 11 if there are no open
   questions.
2. **Prune.** Delete, don't stub:
   - **No code** → delete `skills/qa/`, `skills/pr-queue/`, `skills/review-panel/`,
     `agents/architect.md`, and `PROFILE.md` § 6.
   - **Continuous planning** → delete `skills/sprint-plan/` and `skills/sprint-review/` if the
     operator agreed.
   - **`customer-persona` ungrounded** → leave the agent file in place (it refuses on its own and
     explains how to activate it) but mark it inactive in § 10.
3. **Rewrite each surviving skill's `description:`** to name this company's real PM tool and lanes.
   Descriptions are how skills get routed, and a generic description routes badly. `triage`'s
   description should say "Linear" and the real lane names, not "your PM tool."
4. **Scaffold what's missing**, if the operator agreed: `source-of-truth/STATE.md`, `metrics/aarrr.md`,
   `north-star.md`.
5. **Plugin installs only:** append a short stanza to the working directory's `CLAUDE.md` (creating it
   if absent) pointing at `PROFILE.md`, the read-don't-recall rule, and the gate list. Copy installs
   already have this. **Ask before modifying an existing `CLAUDE.md`** — show the diff first.

---

## Then verify — exercise the real path, don't infer

Run these and report actual results. Do not claim a step passed without running it.

1. `grep -c "{{" PROFILE.md` → must print `0`. Paste the output.
2. Inapplicable sections **deleted**, not `N/A`.
3. **Every path in § 4 resolves.** Check each one. A § 4 pointing at a file that doesn't exist is the
   single most damaging misconfiguration possible — every skill will confidently report nothing.
4. Every agent in `agents/` is referenced by at least one skill; no orphans.
5. Each agent's `model`/`effort` frontmatter matches what § 10 records. **The agent file is
   authoritative** — fix § 10 to match, never the reverse.
6. **Run one lens for real** on a throwaway question from this company's context. Confirm it cites a
   § 4 source rather than giving generic advice.
7. **Prove read-don't-recall works.** Change a figure in one § 4 source, re-run something that reports
   it, confirm the new value comes back, change it back. This is the one test that proves the design
   is actually functioning — do not skip it.

## Then report and stop

```
CONFIGURED — <company>

Placeholders remaining: 0  (grep output pasted)
Deleted: <what and why>
Skill descriptions rewritten: <count>
Scaffolded: <files created>

Verification:
  § 4 paths resolve ........ <n>/<n>   <name any that failed>
  Agents orphaned .......... <n>
  Tier mismatches .......... <n> (fixed in § 10)
  Live lens test ........... <which lens, what it cited>
  Read-don't-recall test ... <figure changed, value that came back>

Open questions (§ 11) — these degrade specific skills until answered:
  1. <question> → degrades <skill>: <how>
  2. …

Inactive lenses: <which, and the one-line path to activating each>

Not automated: nothing here runs on a timer. Every cadence in § 7 needs you to start it,
or an external scheduler you set up separately.

Start with: /agent-panel <a real decision you're facing>
```

**Then stop.** Don't run another skill. The operator drives from here.
