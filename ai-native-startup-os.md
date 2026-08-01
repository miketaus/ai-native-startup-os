# AI-Native Startup OS — Master Runbook & Playbook

> **One file, two halves.** **Part I** is the operating model (the decisions, the prefilled
> stack, the agent-teams design). **Part II** is an executable, Claude-Code-runnable **cascade**
> that rolls the model out *from the leadership team outward to the whole org* in four steps. Run
> Part II in a Claude Code session pointed at your org's GitHub repo; it reads Part I as its spec
> and writes real files (north star, metrics, agent configs, a generated per-user onboarding skill,
> a generated dashboard build-kickoff) into the repo as it goes.

| Field | Value |
|---|---|
| Status | Template — fill every `[BRACKET]` before running |
| Owner | `[ROLE / NAME]` |
| Default stack | **Linear · 1Password · Mixpanel + GA4 · Google Workspace · Slack · GitHub** |
| Identity provider | **Google Workspace** |
| Review cadence | Quarterly |
| Authors | Michael Taus, with Claude (Anthropic) — see [Authors, license & attribution](#authors-license--attribution) |
| License | CC BY 4.0 (docs) + MIT (code) — see end of doc |
| Runnable harness | [`company-os/`](./company-os/) — installs as a Claude Code plugin and configures itself by interview |

> **This playbook is the substrate; the harness is the runtime.** Part II below describes a cascade a
> human facilitates. [`company-os/`](./company-os/) is an installable Claude Code plugin that *executes*
> the same model — it interviews you, writes a `PROFILE.md`, and gives you `/agent-panel` (sixteen
> review lenses) plus skills for planning, triage, blockers, GTM, and review. Where the two differ,
> [`RECONCILIATION.md`](./RECONCILIATION.md) records which wins and why. Roughly 80% of the value is in
> the substrate below; the harness is the 20% that makes it run.

### The cascade at a glance

```
Step 1  EXEC ONBOARDING        leadership wires north-star vision + AARRR metrics +
                               knowledge base + source of truth + tooling + agent teams
            │  (outputs committed to GitHub)
            ▼
Step 2  GENERATE USER ONBOARDING   a role×tier user-onboarding.md is generated from Step 1's
                               outputs and committed to GitHub (one canonical onboarding skill)
            │
            ▼
Step 3  DASHBOARD BUILD KICKOFF    generates a secondary build-kickoff md (committed to GitHub) that a
                               separate session runs to wire real data (Sheets/CRM/Mixpanel/GA) into one
                               unified interface: CEO · Product · Marketing · Sales · CS · Finance · Board
            │
            ▼
Step 4  USER ROLLOUT           provision accounts; each user's first session runs the generated
                               onboarding; per-leader Chief-of-Staff agents pick up their workstreams
```

### Who this is for (and not for)

**A good fit if you are** a startup that already (or is willing to) run on a versioned source of truth
(GitHub), structured work tracking (Linear), a real secrets manager (1Password), and product
instrumentation (Mixpanel/GA4) — **and** whose leaders will actually *approve operating artifacts*
(a north star, a metric dictionary, agent configs) and hold a weekly rhythm. It rewards discipline
about turning private AI output into shared, governed assets.

**Probably not yet for you if** there's no instrumentation or single source of truth, leadership won't
commit to owning a north star and a review cadence, most work is ad-hoc with no tracker, or you need
heavy compliance/SSO/audit on day one (then start with Team/Enterprise, §3). In those cases, do
**Phase 0** (foundations) first, or run only the **Minimum Viable Rollout** (§13.0) until the
prerequisites exist — don't deploy the full architecture into a vacuum.

---

# PART I — THE OPERATING MODEL

## 1. The model in one page

Claude **personal accounts** are the *execution* layer. The *collaboration, governance, and
operating* layer is built around Claude on the prefilled stack:

| Layer | Tool (prefilled) | Role |
|---|---|---|
| Canonical knowledge / source of truth | **GitHub** | Versioned home for prompts, playbooks, agent configs, SOPs, the generated onboarding skill, north-star, metric dictionary, `STATE.md` |
| Living docs / drafts / notetaking | **Google Workspace** (Docs, Drive, Meet/auto-notes, Calendar, Gmail) | Where work is drafted and meetings are captured; finalized output is *promoted* to GitHub |
| Work / workstream tracking | **Linear** | Workstreams = Linear teams/projects; issues, cycles, status |
| Secrets & machine identity | **1Password** (vaults + service accounts + CLI) | Control plane for credentials; never in chat |
| Product + web analytics | **Mixpanel + GA4** | AARRR instrumentation; the metric source feeding the dashboard |
| Comms / digests / promotion notices | **Slack** | Announcements, agent-team digests, "new canonical asset" notices |
| Identity / provisioning | **Google Workspace** | SSO + lifecycle for the *surrounding* tools (not into personal Claude) |

**Success condition.** Not "more people using Claude," but whether individual AI output becomes
**governed, reusable, discoverable, secure** institutional capability — and whether the *leadership
operating layer* (north star, metrics, agent teams, dashboard) is wired first so everything below
it inherits it.

**Two hard gates (read §9):** (1) **Data handling** — consumer plans run on different data terms than
commercial; the data-classification gate (§9.1) decides what may touch personal accounts. (2) **Account
ownership** — a reimbursed personal account belongs to the individual; can't be centrally revoked.

---

## 1.1 The Org North Star — the organizing principle

> **Scope note.** This is the north star for **the product/service the company is building** — the
> company's destination. It is **not** a metric for the Claude rollout. The rollout is just the
> enabling initiative; its own success measures are operational KPIs (§14) and are deliberately *not*
> framed as a north star. Don't conflate the two.

**Leadership sets one Org North Star in Step 1, and the whole multi-agent operating system points at
it.** It has three parts:

1. **Vision** — one sentence: the change the product makes for customers.
2. **North Star Metric (NSM)** — the single measure that best proxies *delivered customer value*
   (not a vanity count). One number, defined once, owned by `analytics-lens`.
3. **Viability constraint (objective function)** — the NSM paired with survival, so growth isn't bought
   at any cost: e.g. *"grow {NSM} **AND** move toward break-even."*

**Everything connects to it — this is the point of the request:**

```
                    ┌──────────────────────────────────────────────┐
                    │  ORG NORTH STAR  (north-star.md)              │
                    │  vision · NSM · viability constraint          │
                    └──────────────────────────────────────────────┘
                          │ is the single reference for ↓
   ┌──────────────────────┼───────────────────────┬─────────────────────────┐
   ▼                      ▼                       ▼                         ▼
 DASHBOARD            CoS AGENTS              RED-TEAM AGENTS            OKRs + USER WORK
 designed around      weigh every             challenge every            every OKR + each user's
 it: CEO tile = NSM,  recommendation by       recommendation against     workstream ladders to a
 each persona view    "does it move the       it: "does this REALLY      lever of the NSM; onboarding
 = one NSM lever      NSM within the          move the NSM, or a         ties the first win to it
 (via AARRR, §12.3)   viability constraint?"  vanity proxy? evidence?"
```

**How each connection works (referenced in the steps + §8):**
- **Dashboard (Step 3 / Appendix C):** *designed around the Org North Star.* The CEO view's headline
  tile **is** the NSM; the NSM decomposes (via AARRR, §12.3) into **levers**, and each persona view is
  the lever its leader owns. No view exists that doesn't ladder to the NSM.
- **CoS agents (§8.2):** carry a **decision rule** — rank and recommend by *expected effect on the NSM,
  subject to the viability constraint.* Anything that doesn't move the north star gets deprioritized
  and flagged.
- **Red-team agents (§8.4):** carry a **north-star test** — their first challenge to any recommendation
  is *"does this actually advance the NSM, or just a proxy that looks good? show the causal evidence."*
- **OKRs + onboarding:** every OKR names the NSM lever it moves; each user's first win and Linear
  workstream tie to their leader's lever, so daily work rolls up to the same destination.

**The rule:** the Org North Star is defined once (Step 1) and **referenced, never redefined** downstream.
`analytics-lens` guards the single NSM definition. This is what turns separate agents, dashboards, and
teams into one coherent operating system aimed at the same place.

---

## 2. The default stack (prefilled)

### 2.1 GitHub — canonical knowledge & source of truth

One **`org-os`** repo (private) is the system of record. Suggested layout (created in Step 1):

```
org-os/
  north-star.md                 vision + objective function (ratified by CEO)
  metrics/aarrr.md              the AARRR metric dictionary (owned by analytics-lens)
  source-of-truth/STATE.md      the live operating snapshot (refreshed by Chief-of-Staff sweeps)
  knowledge-base/
    org-playbooks/  marketing-system/  sales-system/  cs-system/
    product-eng-system/  ops-finance-system/  restricted/
  agents/
    finance-lens.md  analytics-lens.md        (read-only adjudicators)
    cos-ceo.md  cos-product.md  cos-marketing.md  cos-sales.md  cos-cs.md  cos-finance.md
    teams/<workstream>/…                       (per-workstream agent teams)
  onboarding/user-onboarding.md  (generated in Step 2)
  dashboard/dashboard-build-kickoff.md  (generated in Step 3) → produces spec.md + metrics-hub.md
  tooling.md                    stack config: 1P vault map, Linear team map, connector inventory
```

> **Two-track rule.** Engineers work canon directly in GitHub (PRs). Non-technical functions draft in
> **Google Docs** and promote finalized work to GitHub via the promotion path (§6) — they never touch a
> PR. Google Workspace is the friendly front; GitHub is the durable spine.

### 2.2 Linear — workstreams

Each **workstream** is a Linear team or project. The board fields mirror the operating cadence
(Status, Priority, Horizon, Size, Target). Every PR/asset traces to a Linear issue. A leader's
**Chief-of-Staff agent (§8) triages that leader's Linear projects.**

### 2.3 1Password — secrets

Vault tiers: `common` · `function-*` (marketing/sales/cs/product/eng/ops) · `product-*`/`client-*` ·
`automation-*` (service accounts for agents/CI) · `break-glass`. **Credentials are retrieved at runtime
via the 1P CLI; never pasted into chat.** Agent teams that touch external systems use **service accounts**,
not human tokens.

### 2.4 Mixpanel + GA4 — analytics

GA4 = acquisition/web; Mixpanel = product events + AARRR funnel + activation/retention. The
**`analytics-lens` agent (§8) owns the event dictionary** and is the single source of metric-truth that
feeds the dashboard (Step 3). Event names `snake_case object_verb`; categorical values lowercase; an
`app_environment` super-property on every event.

### 2.5 Google Workspace — docs, notetaking, calendar, email, identity

- **Docs/Drive** — drafting surface; finalized → GitHub.
- **Auto-notetaking** (Meet/Gemini notes or your notetaker) — meeting notes land in Drive; the relevant
  **Chief-of-Staff agent ingests them** into `STATE.md` and Linear.
- **Calendar** — drives the operating cadence (sprint/CoS sweep/leadership review triggers).
- **Gmail** — digest delivery + email-triggered workflows (service-account mediated).
- **Workspace as IdP** — SSO + provisioning/offboarding for Linear/GitHub/1P/Mixpanel/Slack.

### 2.6 Slack — comms

Channels for: leadership digests (from CoS agents), `#canon` (new-canonical-asset notices from the
promotion path), per-workstream team updates, and incident/escalation.

---

## 3. Teams vs Personal Max — the decision (condensed)

Score 1–5 per axis, weight, sum. **Decide per population, not once.**

| Axis | ×wt | Pushes toward |
|---|---|---|
| Core workflows touch regulated/client-confidential data? | ×3 | Team/Enterprise |
| Non-power-users (esp. non-technical) depend daily? | ×2 | Team/Enterprise |
| Need SSO / domain capture / role admin / in-product audit? | ×2 | Team/Enterprise |
| Finance needs central caps / pooled credits? | ×1 | Team/Enterprise |
| Need to share Claude projects *inside* Claude? | ×1 | Team/Enterprise |
| Cost-sensitive **and** disciplined about externalizing to GitHub? | ×2 | Personal Max |

**Cost math (use real numbers):** (1) flat seat vs metered API for that user; (2) N personal Max vs N
Team **including backfill cost** (admin, search/RAG layer, audit, promotion-path labor); (3) a
risk-adjusted line for the two hard gates. **Rule of thumb:** personal Max wins for a small, technical,
disciplined group; Team/Enterprise wins as breadth, non-technical dependence, data sensitivity, or
governance load rises. **Mixed is the common end state** — Team for the regulated/non-technical/
collaboration-heavy population, personal Max for technical power users. Keep GitHub + 1Password as the
shared backbone either way.

**Upgrade triggers:** native enterprise search needed · broad non-power-user dependence · centralized
SSO/role admin · direct spend controls · Claude-native governance · data gate (§9.1) requires commercial
terms. Treat the org plan as a **governance accelerator**, not just a collaboration upgrade.

---

## 4. Knowledge architecture

Two access tiers: shared cross-functional (`knowledge-base/*-system`) and tighter
`restricted/` (per product/client, separate 1P scoping). Every promoted artifact carries metadata:

```yaml
owner: ; function: ; audience: ; confidentiality: public|internal|restricted|client-confidential
approved_use: ; last_review: ; references: ; related:
```

First-class objects: prompt packs, approved templates, SOPs, research/competitive memos,
decision-trees/escalation flows, postmortems & call/implementation/incident patterns, automation/
connector specs, repo agent-instruction files, **agent configs**, the metric dictionary, `STATE.md`.

---

## 5. Security & secrets (1Password)

Baseline before scaling past pilot: function- and sensitivity-scoped vaults · least-privilege
**service accounts** (per-vault, expiry where supported) for all machine/agent workflows · CLI/runtime
retrieval (no plaintext) · audit review of access · no personal vaults for org credentials · no
long-lived secrets in repos/shell history/chat. **Non-negotiable:** credentials never enter a prompt.

---

## 6. Governance & promotion path

GitHub is the source of truth. Minimum controls: team-based least-privilege access · protected canonical
content · required review before canonical · validation checks (link/metadata/policy) · named owners
(CODEOWNERS) · request/change templates. **Promotion path:**

```
draft (Google Docs / chat) → clean+templatize → submit to org-os → functional owner review
→ merge as canonical → Slack #canon announce → periodic freshness review
```

All agent-team output is **propose-only**: nothing is published, spent, committed to canon, or sent
externally without the **human gate** (the accountable leader approves).

### 6.0 Two invariants every agent config inherits

**Figures: read, don't recall.** No agent config, prompt, or skill states a current number. Each names
the *path* where the figure lives and its source grade (`A` instrumented and trusted · `B` mostly
right · `C` manual or stale · `D` an estimate). **If a file contradicts the prompt, the file wins** —
and the agent says so. Prompts that recite facts drift out of date and then argue confidently from
stale numbers; prompts that read facts stay correct as the underlying files change.

**Evidence grades: `SAID` < `DID` < `PAID` < `STUCK`.**

| Grade | Means |
|---|---|
| `SAID` | Someone told us — interview, survey, "I'd definitely use that" |
| `DID` | Someone behaved — used it, returned, completed the flow |
| `PAID` | Someone gave up something costly — money, a contract, a migration, a public reference |
| `STUCK` | They'd be hurt if it went away — retention through a price rise, a workaround built on us |

State the grade with the claim. **Never plan on `SAID` alone** — and note that counting how many people
said something does not upgrade it. Name the cheapest test that would move it to `DID` or `PAID`.

### 6.1 Operating cadences (hard defaults — tune per org)

| Rhythm | Default | Owner |
|---|---|---|
| **CoS sweep** (triage workstreams, refresh `STATE.md` slice, post digest) | **Weekly** | each leader's CoS |
| **Canonical-asset approval SLA** (review → merge or reject a promotion-path submission) | **≤ 48 h** | functional owner / steward |
| **Knowledge freshness review** (re-check canonical assets for staleness) | **Monthly** | knowledge steward (per function) |
| **Plan-tier + technical-tier review** (right plan/surface per role; promote tiers) | **Quarterly** | leader + IT |
| **Connector / service-account access review** (sprawl + least-privilege check) | **Quarterly** | Security |
| **Leadership review** (NSM + lever movement, cross-team blockers) | **Weekly** | CEO |

A cadence with no owner doesn't happen — every row names one. Cadences are driven off Google Calendar
triggers; the CoS agents prepare the inputs ahead of each.

---

## 7. Deploy by technical tier (high / mid / low)

| | High (builders) | Mid (power users) | Low (operators) |
|---|---|---|---|
| Who | Engineers, data, technical PMs | Most PMs, analysts, ops, mktg ops | Sales, CS, exec, most marketing, G&A |
| Surface | **Claude Code** + app | **App + Projects** (+ Claude Code for narrow tasks) | **App + pre-built Projects + templates** |
| Enable | MCP, agents, service accounts, repo agent-files | Curated connectors, Projects w/ org knowledge | Pre-built Projects, prompt packs, copy-paste |
| First win ≤30 min | Refactor/test a real ticket | Draft from a real doc; build one Project | Rewrite one real email/brief from a template |
| Ramp | 1–2 wks | 2–4 wks | 2–6 wks (templates carry them) |
| Guardrail focus | Service-account discipline, connector review | Data gate, promotion path | Data gate, never paste secrets/PII |

Tier ≠ seniority (a VP of Sales is usually "low," and that's fine). Re-assess at 30/90 days.

---

## 8. Agent teams — Chief-of-Staff per leader + a team per workstream

This is the layer that makes the rollout *operate*, not just exist. Three agent classes, all
**propose-only behind a human gate**.

### 8.0 What "agent teams" are (and aren't)

> Reference: **Claude Code — Orchestrate teams of Claude Code sessions**, https://code.claude.com/docs/en/agent-teams

An **agent team** is several Claude Code instances working together: **one session is the lead**
(coordinates work, assigns tasks, synthesizes results) and the rest are **teammates**, each a *full,
independent Claude Code session with its own context window*. They coordinate through two shared
mechanisms: a **shared task list** (work items teammates claim/complete, with dependencies) and a
**mailbox** (teammates message each other directly — not only the lead). You can talk to any teammate
directly, not just through the lead.

**How this differs from the other two things this doc uses the word "agent" for:**

| | Subagent | Agent team | (For contrast) this doc's "agents" |
|---|---|---|---|
| What | Helper spawned inside one session | Multiple full sessions: 1 lead + teammates | A reusable role definition (a config file) |
| Communication | Reports results back to the caller only | Teammates message **each other** directly | n/a — it's a spec, not a runtime |
| Coordination | Main agent manages all work | **Shared task list**, self-claimed | n/a |
| Best for | Focused tasks where only the result matters | Work needing discussion/challenge/parallel ownership | Defining `cos-*`, `*-lens`, `red-team` once, reused as a subagent *or* a teammate |
| Token cost | Lower (summarized back) | **Higher** (each teammate is a separate instance) | — |

So in this playbook: the **CoS / lens / red-team / workstream specs in `agents/` are *role
definitions*** (subagent definitions). A leader **runs an agent *team*** when a workstream needs
parallel, collaborating sessions — the CoS (or a workstream lead) acts as the **team lead** and spawns
teammates *using those role definitions* (a subagent definition can be referenced by name to spawn a
teammate; it honors that definition's tool allowlist and model).

**Operational notes (from the docs, current as of the referenced page):**
- **Experimental + off by default** — enable with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` in
  `settings.json`/env. Without it, no team is set up.
- **Best for** research/review, new modules, competing-hypothesis debugging, cross-layer work where
  teammates own **different files**. Not for sequential work, same-file edits, or heavy dependencies —
  use a single session or subagents there.
- **Team size:** start with **3–5 teammates**, ~5–6 tasks each. Avoid file conflicts (one owner per file
  set). One team per session; no nested teams; the lead is fixed for the session.
- **Cost:** materially more tokens than one session — reserve for work where parallel exploration earns it.
- **Quality gates** can be enforced with hooks (`TaskCreated`, `TaskCompleted`, `TeammateIdle`) — this is
  where the **human gate** and lens/red-team checks below are wired.

**Vocabulary (used from here on, to keep "agent" from carrying three meanings):**
- **Role spec** — a reusable role *definition* (a config file in `agents/`, e.g. `cos-product.md`). Static.
- **Runtime team** — a live *agent team* (lead + teammates) spawned from role specs to do work. Dynamic.
- **Adjudicator** — a read-only role spec that is a single source of truth: `analytics-lens` (metrics),
  `finance-lens` (money). Consulted, never overruled silently.

So: a leader assembles a **runtime team** from **role specs**, and any consequential output is checked by
the **adjudicators** and red-team before the human gate.

### 8.1 Adjudicators (read-only role specs, single source of truth) — 2

- **`analytics-lens`** — the single source of **metric-truth**. Owns `metrics/aarrr.md`, defines
  activation/qualified-lead/retention, guards against vanity metrics and p-hacking, validates the
  dashboard's numbers. Read-only.
- **`finance-lens`** — the single source of **money-truth**. Owns unit economics, CAC/LTV/payback,
  burn/runway; judges whether any proposal moves toward or away from break-even. Read-only.

Every plan, experiment, and spend decision is checked against these two before the human gate.

### 8.2 Chief-of-Staff agent — **one per leader** (CEO, Product, Marketing, Sales, CS, Finance)

Each leader gets a CoS agent customized to their function, metrics, and cadence. It:

- **Triages all of that leader's workstreams** (their Linear projects) — ranks by priority/impact,
  flags blockers and cross-team dependencies, proposes the next 1–3 items.
- **Maintains the leader's slice of the source of truth** — refreshes their section of `STATE.md` from
  Linear + Google auto-notes + analytics.
- **Prepares the leader's dashboard view + Slack digest** on the calendar cadence.
- **Routes work to the right workstream agent team (§8.3)** and consults `analytics-lens`/`finance-lens`
  before recommending.
- Is **propose-only**: it drafts the priorities/digest/plan; the leader approves.

CoS config (generated per leader in Step 1, stored at `agents/cos-<role>.md`):

```markdown
---
name: cos-[role]
description: Chief-of-Staff agent for the [role] leader. Triages their workstreams,
  maintains their source-of-truth slice, prepares dashboard + digest. Propose-only.
---
OWNER: [leader name]
NORTH-STAR LEVER: [the ONE AARRR lever of the NSM this leader owns, from §1.1, e.g. "Activation"]
OBJECTIVE FUNCTION: [leader's north-star KRs, cascaded from north-star.md — each must move the lever above]
WORKSTREAMS (Linear): [project A], [project B], …   (each ladders to the lever)
KEY METRICS (from metrics/aarrr.md): [the 3–5 this leader owns — all inputs to the NSM]
CADENCE (Google Calendar): weekly sweep [day]; leadership review [day]
INPUTS: Linear [projects]; Google Drive auto-notes [folder]; Mixpanel/GA [dashboards]
DECISION RULE: weigh and rank everything by its **expected effect on the Org North Star (§1.1) within
  the viability constraint**. Deprioritize and flag anything that doesn't move the NSM lever this leader
  owns. When two options tie on the NSM, break toward the cheaper/faster one (finance-lens).
OUTPUTS (propose-only): updated STATE.md slice; ranked priorities (each annotated with its NSM impact);
  Slack digest to [channel]; dashboard view refresh
GATES: consult analytics-lens for any metric claim; finance-lens for any spend; route consequential
  recommendations through red-team (north-star test) before the human gate; human approval before publish
ESCALATION: [when to flag to CEO / cross-functional]
```

### 8.3 Workstream agent teams — **one per workstream, customized**

Any workstream can run its own **agent team** (§8.0 — lead + teammates, shared task list, mailbox;
https://code.claude.com/docs/en/agent-teams), scoped to its Linear project and KB space. The leader's
CoS agent dispatches to these; the team lead spawns teammates from the `agents/` role definitions.
Examples (tune per org):

| Function | Example workstream team (lead + teammates) |
|---|---|
| Product | PM-lead + user-research + spec-writer + QA-reviewer |
| Engineering | tech-lead orchestrator + builder agents (isolated worktrees) + code-reviewer + QA |
| Marketing | campaign-lead + content + SEO + analytics (consults analytics-lens) |
| Sales | sales-lead + account-research + proposal-writer + objection-coach |
| Customer Success | cs-lead + onboarding-planner + QBR-drafter + risk-analyst |
| Finance/Analytics | analyst-lead + metric-definition (= analytics-lens) + model-builder (consults finance-lens) |

**Guardrails (carried from the build doctrine):** one session per checkout (isolated git worktrees so
teams don't switch branches under each other) · narrow file lanes · small frequent merges · cross-team
dependencies declared up front · QA is a **gate**, never a build teammate · everything propose-only.

### 8.4 Red-team agent — optional, per user (and per leader/workstream)

An **opt-in challenge agent** any user can enable to drive better thinking. Its job is to *disagree
well*: surface unstated assumptions, argue the strongest counter-case, stress-test plans, name failure
modes and second-order effects, and check for confirmation bias — **before** a decision is committed.
It is a thinking partner, not a blocker; the human still decides.

**Its first and sharpest challenge is the north-star test (§1.1):** *"Does this recommendation actually
advance the Org North Star — or just a proxy/vanity metric that looks like progress? And is it worth it
under the viability constraint?"* It demands the causal evidence, not the story. This is what keeps the
whole system honest: CoS agents *propose* toward the north star, red-team *pressure-tests* that the
proposal really gets there.

- **Per user:** offered as an optional setup in onboarding (Appendix A, Phase 5). A low-tier user gets a
  lightweight "devil's advocate" Project; a high-tier user gets a `red-team` Claude Code skill.
- **Per leader / workstream:** the CoS agent can route a plan through `red-team` before the human gate;
  workstream teams can add a red-team seat so proposals are pre-challenged.
- **Pairs with the lens agents:** `red-team` challenges *reasoning*; `analytics-lens`/`finance-lens`
  challenge *numbers*. Use together on anything consequential.

Config (`agents/red-team.md`, optional):

```markdown
---
name: red-team
description: Optional challenge/devil's-advocate agent. Surfaces assumptions, argues the
  counter-case, names failure modes and biases. Improves decisions; does not block them.
---
MODE: skeptical but constructive. Always end with "what would change my mind" + the 1–2 highest-leverage tests.
NORTH-STAR TEST (always run first): does this actually move the Org North Star (§1.1 NSM), or only a
    proxy/vanity metric? Is the NSM gain worth it under the viability constraint? Demand causal evidence.
DO: run the north-star test; list unstated assumptions; give the strongest opposing case; name top 3
    failure modes + likelihood; flag confirmation/sunk-cost/availability bias; ask for disconfirming evidence.
DON'T: nitpick, block, or moralize. Stay on the decision at hand. Defer the final call to the human.
INVOKE FOR: strategy, spend, launches, hiring, any "we're sure about this" moment.
PAIRS WITH: analytics-lens (metric truth), finance-lens (money truth), cos-* (which proposes toward the NSM).
```

---

## 9. Gaps → recommendations (risk register)

| # | Gap | Recommendation |
|---|---|---|
| R1 | Data terms on personal accounts | Ratify the data gate (§9.1) before rollout; embed in onboarding; forbid restricted/client-confidential on personal accounts; re-verify plan terms quarterly. |
| R2 | Personal-account ownership/continuity | Corporate-email-only sign-up; AUP (output = org IP); offboarding checklist (no data retained, connectors revoked); track in AI tool registry; prefer org plan where continuity is critical. |
| R3 | Knowledge fragmentation | Enforce the promotion path (§6); measure reuse (§14); name a knowledge steward per function. |
| R4 | Connector / MCP / agent sprawl | Central connector approval; service-account discipline; log high-risk automation; quarterly inventory. |
| R5 | Non-technical adoption failure | Two-track SoR (Google Docs front / GitHub spine); templates as the product for low-tier; onboard on real work. |
| R6 | Partial audit | State limit to leadership; combine GitHub + 1P + endpoint logs + attestations; full audit = upgrade trigger. |
| R7 | Learning-curve / tier mismatch | Deploy by tier (§7); honest personalized onboarding (Step 2/4); 30/90-day re-assessment. |
| R8 | Cost asserted not proven | Run the three-way cost math (§3) incl. backfill + risk-adjusted; plan tier per role. |
| R9 | Spend scatter | Virtual cards w/ per-seat limits + monthly attestations; Finance owns one AI-spend line; manager approval for Max. |
| R10 | Agents acting unsupervised | All agents propose-only behind a human gate; lens adjudicators consulted before any metric/spend claim. **One narrow exception, which is not an action:** `compliance-lens` may veto a *ship verdict* on sensitive-surface changes — it changes what the panel recommends, never what happens. Recorded with its reason; the operator may override explicitly and in the open. See R11. |
| R11 | Prompts arguing from stale numbers | **Figures: read, don't recall.** No prompt, skill, or agent config states a current figure; each names the *path* where the figure lives plus a source grade (`A`–`D`). If a file contradicts the prompt, the file wins. Re-verified by the `state-sweep` cadence (§ 6.1). |
| R12 | Planning on opinion dressed as evidence | Grade every decision-driving claim `SAID` < `DID` < `PAID` < `STUCK`, and state the grade with the claim. Reject planning that rests on `SAID` alone; name the cheapest test that would upgrade it. |

### 9.1 Data-classification gate

```
public / internal               → personal accounts OK
restricted                      → personal accounts only with named approval + scoped controls
client-confidential / regulated → org (commercial) plan required; NOT on personal accounts
```

---

# PART II — THE CASCADING ONBOARDING (run in Claude Code)

> Run these steps in order in a Claude Code session pointed at the `org-os` repo. Each step has a
> **human gate** — a named leader approves before the next step. Steps write real files into the repo;
> the cascade is what carries the leadership decisions down to every user.
>
> **These Steps are the *mechanics*; the *sequence* is the Phase 0–4 maturity model (§13).** Map: Step 1+2
> = Phase 1 · Step 4 (one function) = Phase 2 · Step 3 = Phase 3 · Step 4 (org-wide) = Phase 4. If you only
> want a first taste, run the **Minimum Viable Rollout (§13.0)** — Step 1 with a single CoS, no dashboard.

## Step 1 — Exec onboarding (wire the operating layer)

**Who:** CEO + functional leaders, facilitated in one Claude Code session.
**Goal:** turn leadership intent into committed, machine-readable operating assets.

**Claude Code actions:**
1. **Org North Star (§1.1).** Interview the CEO; write `north-star.md` — the **product/service** north
   star: its **vision** (one sentence), the **North Star Metric (NSM)** (the single proxy for delivered
   customer value), and the **viability constraint / objective function** (e.g. "grow {NSM} AND toward
   break-even"); plus 3–5 company OKRs **each tied to a named lever of the NSM**, and the
   **data-classification gate** (§9.1). This file is the reference the dashboard, CoS agents, and
   red-team all point at — get it right first.
2. **AARRR metrics.** With `analytics-lens`, write `metrics/aarrr.md` — show the **NSM and how it
   decomposes into the AARRR levers** (Acquisition, Activation, Retention, Revenue, Referral); define the
   **Value Moment**; **map every metric to a Mixpanel event or GA4 dimension**; assign each lever an
   owning function. This decomposition is the spine the CoS agents and dashboard both read.
3. **Knowledge base + source of truth.** Scaffold `knowledge-base/` spaces (§2.1) and seed
   `source-of-truth/STATE.md` with the current snapshot per function.
4. **Tooling.** Write `tooling.md` — the 1P vault map, Linear team/workstream map, Mixpanel/GA
   property conventions, Slack channel map, connector inventory. Confirm Google Workspace SSO + the
   provisioning/offboarding checklist (R2).
5. **Agent teams.** Generate `agents/finance-lens.md`, `agents/analytics-lens.md`, and a
   **`agents/cos-<role>.md` per leader** (§8.2 template, filled from steps 1–2), plus stubs for the
   priority workstream teams (§8.3), and the optional `agents/red-team.md` (§8.4) if leadership opts in.
6. **Plan/tier assignment.** Apply §3 + §7: record, per role, the **plan tier** (Pro/Max/Team) and
   **technical tier** (high/mid/low) in `tooling.md`.
7. **Optional methodology modules.** Decide which of the §12 modules (Lean Startup experimentation,
   customer discovery/research, data-driven decisioning, Persona-Value Matrix) the org adopts; record the
   choice in `north-star.md` so Step 2 wires the chosen ones into onboarding and Step 3 reflects them in
   the dashboard (e.g. an experiment-tracking view).

**Human gate:** CEO ratifies `north-star.md` + the data gate; each leader approves their `cos-<role>.md`.
**Outputs committed to GitHub:** north-star, AARRR dictionary, STATE.md, KB scaffold, tooling.md, lens +
CoS agent configs, plan/tier matrix.

### Step 1 acceptance criteria (pass/fail — don't proceed to Step 2 until all green)

Treat these as a review checklist, not aspirations. Any ✗ blocks the gate.

**`north-star.md` — valid when:**
- ☐ Exactly **one** NSM, and it measures *delivered customer value* (not a vanity count like signups/logins).
- ☐ Vision is one sentence; viability constraint is explicit (the "AND toward …" half).
- ☐ Every OKR/KR names the **NSM lever** it moves; no orphan OKRs.
- ☐ Data-classification gate filled for all three tiers with real examples.
- ☐ *Anti-patterns:* NSM is revenue-only (lagging) · two "north stars" · no viability constraint · KRs with no lever.

**`metrics/aarrr.md` — valid when:**
- ☐ NSM decomposition shown (NSM = its levers); each lever has an **owning function**.
- ☐ **Every** metric maps to a specific Mixpanel event or GA4 dimension (no "TBD").
- ☐ One definition per metric; the Value Moment + current OMTM are named.
- ☐ *Anti-patterns:* a metric defined two different ways · "active" = logged-in · all-time (uncohorted) funnels.

**`tooling.md` — valid when:**
- ☐ Every role has a **plan tier** (Pro/Max/Team) *and* a **technical tier** (high/mid/low).
- ☐ 1P vault map, Linear workstream map, Slack channels, connector inventory all present.
- ☐ Google Workspace SSO confirmed; offboarding checklist (R2) present.
- ☐ *Anti-patterns:* a role on a personal account that handles client-confidential data (violates §9.1).

**Agent role specs — valid when:** each `cos-<role>.md` names its NSM lever + decision rule; both
adjudicators present; every spec is propose-only with a human gate. (Red-team optional.)

## Step 2 — Generate the user-onboarding skill (→ GitHub)

**Goal:** a single canonical onboarding skill, prefilled with *this org's* real links and metrics, so
every user's first session is honest, role-aware, and carries the org invariants.

**Claude Code actions:**
1. Read Step 1's outputs and fill the onboarding skill template (Appendix A) with real values:
   GitHub KB links, 1P retrieval pattern, the data gate text, north-star/AARRR references, the
   role×tier table, the relevant CoS agent per function.
2. Write `onboarding/user-onboarding.md` and commit. (Optionally emit per-function variants
   `onboarding/user-onboarding-<function>.md`.)
3. Register it: in `tooling.md`, note that each new user's first Claude session (or Claude Code skill,
   or Project instructions) loads this file.

**Human gate:** the knowledge steward + one leader review the generated onboarding for honesty and
correct links.
**Output:** `onboarding/user-onboarding.md` in GitHub — the artifact Step 4 deploys.

## Step 3 — Cross-functional dashboard: generate the build kickoff (→ GitHub)

**Goal:** rather than build the dashboard inline, this step **generates a secondary, self-contained
build-kickoff md** (just as Step 2 generates the onboarding md) that a separate session — or your
data/BI person — executes to stand up one unified interface over real sources for **CEO · Product ·
Marketing · Sales · CS · Finance/Analytics · Board**. The dashboard is **designed around the Org North
Star (§1.1):** the CEO headline tile *is* the NSM and each persona view *is* one of its levers — so the
whole company reads from the same destination. Definitions owned by the lens agents.

**Claude Code actions:**
1. Read Step 1's outputs (`north-star.md`, `metrics/aarrr.md`, `tooling.md`, the CoS configs).
2. Fill the **dashboard build-kickoff template (Appendix C)** with this org's real sources, metric
   definitions, persona views, and implementation choice.
3. Write **`dashboard/dashboard-build-kickoff.md`** and commit. This file is the deliverable of Step 3 —
   a standalone runbook another Claude Code session can pick up to do the actual wiring.
4. The kickoff md itself specifies producing `dashboard/metrics-hub.md` (the single join layer) and
   `dashboard/spec.md` (the per-persona views) when it is executed.

**Human gate:** leadership reviews the kickoff (sources, persona views, who builds it); `analytics-lens`
+ `finance-lens` confirm the metric/money definitions it carries before the build session runs.
**Output:** `dashboard/dashboard-build-kickoff.md` in GitHub — which, when run, produces the metrics hub,
the per-persona views, the live dashboard, and the board export.

## Step 4 — User rollout (cascade to the org)

**Goal:** every user starts with the honest, personalized onboarding while inheriting the leadership layer.

**Claude Code actions / runbook:**
1. **Provision** (per the Step 1 tier matrix): Google Workspace email → Claude plan (Pro/Max/Team) →
   1Password vault access → Linear team → relevant `knowledge-base/*-system` spaces → Slack channels.
2. **Kick off onboarding:** the user's first Claude session loads `onboarding/user-onboarding.md`
   (as a Project's instructions for low/mid tiers, or a Claude Code skill for high tier). It runs the
   honest intake → calibrate → personalized starter kit → one real first win → tier-sized habits.
3. **Connect to the operating layer:** the user's work lands in their Linear workstream; their leader's
   **`cos-<role>` agent picks it up** in the next sweep; reusable output flows up the promotion path to
   GitHub and appears in `#canon`.
4. **Checkpoints:** 30/90-day re-assessment of tier + whether the user is hitting their stated 30-day
   outcome (measured per §14).

**Human gate:** manager confirms provisioning + tier; user acknowledges the data gate + account-ownership
policy (R2) during onboarding.

**The cascade is complete:** leadership's north star, metrics, agents, and dashboard now flow down into
every user's daily surface — and every user's output flows back up into the same source of truth.

---

## 10. Backfilling Team/Enterprise (since default is personal-first)

Central identity → Google Workspace + provisioning checklists + AI tool registry. **SSO into Claude →
not fully backfillable** (require corporate email, MDM, browser policy, AUP). Role-based Claude perms →
GitHub + 1P scopes + SOPs. Spend control → virtual-card limits + attestations + Max approval. Enterprise
search → GitHub canon (+ optional RAG later). Connectors → controlled workflows, no sprawl. Collaboration
→ GitHub + Linear + Slack + Google. Audit → GitHub + 1P + endpoint logs (**partial**). State the
irreducible gaps (SSO, native search, in-product audit) to leadership explicitly.

---

## 11. RACI

| Activity | R | A | C | I |
|---|---|---|---|---|
| Model (personal/Team/mixed) | CTO/CIO | CIO | Finance, Security, leads | All |
| Data gate (§9.1) | Security | CISO/CIO | Legal, leads | All |
| GitHub canon & governance | KM/CTO | CTO | Leads | All |
| 1P vault design | Security/IT | CISO | Eng, app owners | Users |
| North star + AARRR (Step 1) | analytics-lens + leads | CEO | finance-lens | All |
| Onboarding skill (Step 2) | KM/enablement | CTO | Leads | New users |
| Dashboard (Step 3) | analytics-lens + finance-lens | CFO/CEO | Leads | Board |
| CoS + workstream agents (§8) | each leader | CEO | lens agents | Teams |
| Spend & attestations | Finance | CFO/CIO | Managers | Leadership |
| 30/90-day adoption review | Lead | CTO | Stewards | Leadership |
| Red-team agent (§8.4) | enabling user/leader | CEO | — | Team |
| Methodology modules (§12) | each leader | CEO | analytics-lens, finance-lens | All |

---

## 12. Optional methodology modules (org-wide, opt-in)

These four are **optional onboarding modules** the org can switch on (Step 1 action 7). Each is a
shared way of *working and deciding*, not a tool. When adopted, Step 2 wires its prompts/templates into
onboarding, the relevant CoS + lens agents enforce it, and it gets a home in `knowledge-base/org-playbooks`.
Adopt as a set for the strongest effect — they reinforce each other (discover → hypothesize → experiment →
decide on evidence → target the right persona/value).

### 12.1 Lean Startup experimentation

**Summary.** Treat the business as a series of testable hypotheses. Run tight **Build → Measure → Learn**
loops, ship the smallest thing that tests the riskiest assumption (an MVP), and pursue **validated
learning** — pivot or persevere on evidence, not opinion. In this stack: hypotheses live as Linear
issues, the MVP ships through the normal build flow, Mixpanel/GA measure the result, and `red-team`
pre-challenges the hypothesis while `analytics-lens` defines its success metric.
**Authoritative sources:** Lean Startup principles — https://theleanstartup.com/principles ·
Steve Blank — https://steveblank.com

### 12.2 Customer discovery / research

**Summary.** Get out of the building: talk to customers to learn their problems *before* building
solutions. Ask about real past behavior, not hypotheticals ("The Mom Test"); run **continuous discovery**
(small, frequent interviews feeding decisions) rather than one-off research. In this stack: interview
notes land in Google Drive (auto-notetaking) → the CoS agent distills patterns into
`knowledge-base/*-system` → recurring patterns become playbooks; **no customer PII on personal accounts** (§9.1).
**Authoritative sources:** The Mom Test (Rob Fitzpatrick) — https://www.momtestbook.com ·
Continuous Discovery (Teresa Torres) — https://www.producttalk.org/continuous-discovery/

### 12.3 Data-driven decisioning

**Summary.** Decide on evidence with an explicit metric model. Use the **AARRR / "Pirate Metrics"**
funnel (Acquisition, Activation, Retention, Revenue, Referral) and pick **One Metric That Matters** for
the current stage (Lean Analytics). This is the same AARRR you wired in Step 1 — this module makes
*using* it a habit: every proposal cites the metric it moves, `analytics-lens` guards definitions and
significance, `finance-lens` guards the money, and the Step 3 dashboard is the shared evidence base.
**Authoritative sources:** Startup Metrics for Pirates / AARRR (Dave McClure) — https://500.co ·
Lean Analytics (Croll & Yoskovitz) — https://leananalyticsbook.com

### 12.4 Persona-Value Matrix approach

**Summary.** Map each target **persona** against the **value** your offering delivers them — the jobs
they're trying to get done, their pains, and the gains you create (Value Proposition Canvas; Jobs-to-be-
Done). The matrix becomes the spine of GTM: marketing/sales/CS tailor messaging per persona×value cell,
and experiments (12.1) are run per cell. In this stack it lives in `marketing-system`/`sales-system`,
the marketing/sales CoS agents keep it current, and dashboard views can slice the funnel by persona.
**Authoritative sources:** Value Proposition Canvas (Strategyzer) —
https://www.strategyzer.com/library/the-value-proposition-canvas ·
Jobs to Be Done (Christensen, HBR) — https://hbr.org/2016/09/know-your-customers-jobs-to-be-done

---

## 13. Rollout maturity model (Phase 0–4)

You do **not** need the full architecture on day one. Adopt in phases; each has an entry bar and an exit
gate, and the executable mechanics are the Part II Steps. **Don't start a phase until the prior one's exit
gate is green.**

### 13.0 Minimum Viable Rollout (start here)

The smallest version that still earns its keep — time-to-first-value in **days, not weeks**:

- **one** `org-os` repo · **one** `north-star.md` (vision + NSM + viability constraint) · **one**
  `metrics/aarrr.md` · **one** CoS role spec (for **CEO or Product**) · **one** `user-onboarding.md`.
- **No dashboard, no workstream runtime teams, no red-team yet.** Add those at Phases 2–4 as the value
  becomes obvious. The MVP = Phase 0 (minimal) + a single-CoS Phase 1.

This preserves the whole architecture's *shape* (north star → CoS → onboarding → promotion path) while
deferring everything that adds setup cost before there's proof it helps.

### 13.1 The phases

| Phase | Goal | Mechanics (Part II) | Exit gate |
|---|---|---|---|
| **0 — Foundations** | Prereqs exist; no agents yet | Decide model (§3); ratify data gate (§9.1); create `org-os` + 1P vaults + Linear/Slack/analytics conventions; name stewards | Appendix B checklist green |
| **1 — Leadership pilot** | Wire the operating layer | **Step 1 + Step 2** (Org North Star, AARRR, KB, tooling, adjudicators + CoS specs; generate `user-onboarding.md`) | **Step 1 acceptance criteria** all green · _MVP can stop here_ |
| **2 — Function pilot** | One department end-to-end | **Step 4 scoped to one function** (Eng or CS): onboard its users, stand up its CoS + one runtime team, run the promotion path | That function promotes to canon weekly + hits the onboarding KPI; friction in the promotion path resolved |
| **3 — Dashboard integration** | Shared evidence base | **Step 3** (generate the dashboard build-kickoff → metrics hub + persona views) | CEO NSM tile live, lens-validated; zero conflicting metric definitions |
| **4 — Scaled rollout + hardening** | Whole org + governance | **Step 4 org-wide**; audit routines; tighten scopes; human-approval gates on high-risk; selective Team adoption (§3.4) | Coverage KPI met; quarterly reviews running (§6.1) |

> **Why a function pilot (Phase 2) before going wide:** piloting one department through the *entire* loop
> (onboarding → workstream team → promotion path → dashboard slice) surfaces the real friction in a
> contained team before the whole company hits it. Refine the promotion path there first.

---

## 14. Rollout operational KPIs (NOT a north star)

> These measure whether the **Claude rollout** is working — they are operational KPIs only, deliberately
> **not** framed as a north star. The only north star in this system is the **product's** (§1.1). Don't
> let rollout adoption metrics masquerade as the company's destination.

% finalized output **promoted** to canon · **artifact reuse rate** · **time-to-find** a canonical asset
· **secret hygiene** (zero plaintext; % automation on service accounts) · **spend predictability**
(variance/active user) · **coverage** (% functions with a current owned space + a live CoS agent) ·
**onboarding effectiveness** (% users hitting their 30-day outcome) · **dashboard trust** (single-
definition adherence — zero conflicting metric definitions across persona views).

---

## Appendix A — The user-onboarding skill (template Step 2 fills in)

````markdown
---
name: user-onboarding
description: Honest, role- and skill-aware onboarding for a new Claude user that delivers a
  fast first win and a personalized starter kit while enforcing org invariants.
---
# ROLE
You are an onboarding guide for a new Claude user at [ORG]. Get this ONE person productive fast,
honestly, and safely — tuned to their role and technical skill — without losing org standards.
Warm, concrete, never hype. Under-promise rather than oversell.

# NON-NEGOTIABLE ORG INVARIANTS (enforce for everyone)
1. DATA GATE: only [public/internal] data here; NEVER [restricted/client-confidential/regulated] —
   that requires [commercial surface]. If the user describes such data, stop and redirect.
2. SECRETS: never accept/request passwords, keys, tokens, PII. Point to [1Password + CLI pattern].
3. PROMOTE: reusable output goes to [GitHub org-os KB links] with the metadata block [LINK].
4. OWNERSHIP: account uses [Google Workspace email]; output is org IP; offboarding policy [LINK]. Confirm.
5. START FROM CANON: prefer org templates/prompt packs [LINKS] over reinventing.

# PHASE 1 — HONEST INTAKE (ask, then listen)
a) Function? (product/eng/marketing/sales/cs/operations/other)
b) Technical comfort, honestly (no wrong answers — only changes your tools):
   HIGH=terminal/IDE+code · MID=apps+light scripting, happy to configure · LOW=office apps, no install.
c) Your 2–3 most time-consuming tasks? d) What data do they involve? e) Great 30-day outcome?

# PHASE 2 — CALIBRATE & SET HONEST EXPECTATIONS
Confirm tier+surface (HIGH→Claude Code; MID→app+Projects; LOW→app+pre-built Projects+templates).
Apply the data gate to (d). Name 2–3 things Claude is great at for their role AND 1–2 it's unreliable at.

# PHASE 3 — PERSONALIZED STARTER KIT
Their surface + day-to-day use; 3–5 role first-wins (table below); 3–5 canonical prompts/templates
[links]; their KB spaces + how to contribute back; secrets pattern at their depth; the data gate restated.

# PHASE 4 — DO ONE REAL TASK NOW
Take the highest-value Phase-1(c) task that passes the gate; do it together end-to-end; produce a real
artifact; if reusable, show how to promote it. Then: their work goes in Linear [team]; their leader's
[cos-<function>] agent will pick it up.

# PHASE 5 — HABITS & CHECKPOINTS
3 tier-sized habits (LOW: start from a template; MID: save winning chats as Projects; HIGH: agent-
instruction files + service accounts, never personal tokens). Where to get help [link]. 30-day self-check
against (e); offer tier re-assessment.

# OPTIONAL ADD-ONS (offer, don't force)
- RED-TEAM AGENT: offer to set up a challenge/devil's-advocate partner (LOW=a Project; HIGH=the
  `red-team` skill) for stress-testing decisions. Explain it improves thinking, doesn't block. [link to agents/red-team.md]
- METHODOLOGY MODULES (only those the org adopted in Step 1): briefly orient the user to any of —
  Lean Startup experimentation, customer discovery/research, data-driven decisioning, Persona-Value
  Matrix — that apply to their role, with the org's playbook link. [links to knowledge-base/org-playbooks]

# STYLE
Concrete over abstract. Never invent links/policies — if a [BRACKET] is empty, say so and name the owner.
Earn trust by being honest, including about limitations.

# ROLE × TIER TABLE (Step 2 fills with real links)
# Product   | PRD from notes; synthesize interviews; decision→ADR     | [product-eng-system] | won't replace user contact; verify data
# Eng       | refactor+tests; explain code; draft ADR                 | [product-eng-system] | can hallucinate APIs; run/review; secrets out
# Marketing | repurpose 1→3 channels; tighten to persona; outline     | [marketing-system]   | brand voice needs edit; fact-check
# Sales     | pre-call brief; follow-up from notes; objection script  | [sales-system]       | no CRM writes from chat; verify facts
# CS        | onboarding plan; QBR draft; risk summary                | [cs-system]          | no customer PII; confirm specifics
# Ops/G&A   | draft SOP; synthesize meeting; redline policy           | [ops-finance-system] | legal/finance needs human sign-off
````

## Appendix B — Decisions to fill before running

- [ ] §3 scorecard + cost math → model per population (personal/Team/mixed)
- [ ] Data gate (§9.1) ratified · personal-account ownership policy written (R2)
- [ ] Plan tier + technical tier per role recorded in `tooling.md`
- [ ] `org-os` repo created with the §2.1 layout
- [ ] 1P vaults · Linear teams · Mixpanel/GA properties · Slack channels mapped
- [ ] Knowledge stewards named per function
- [ ] Step 1 human gate owners identified (CEO + each leader)
- [ ] Red-team agent (§8.4) — opted in? · Methodology modules (§12) — which adopted?

## Appendix C — The dashboard build-kickoff (template Step 3 fills in → `dashboard/dashboard-build-kickoff.md`)

````markdown
---
name: dashboard-build-kickoff
description: Standalone runbook to build [ORG]'s cross-functional dashboard. A separate Claude Code /
  BI session executes this. Definitions are owned by analytics-lens (metrics) and finance-lens (money).
---
# OBJECTIVE
Stand up ONE unified interface over real data for: CEO · Product · Marketing · Sales · CS ·
Finance/Analytics · Board. **The CEO view's headline IS the North Star Metric (§1.1); each persona view
is that leader's NSM lever.** One definition per metric (no conflicting numbers across views).

# SOURCES TO CONNECT (fill in real handles)
- Product/web events: Mixpanel [project] + GA4 [property]
- Revenue/pipeline: CRM [system + auth via 1P service account]
- Delivery: Linear [teams/projects]
- Manual/finance: Google Sheets [hub link]

# BUILD ACTIONS
1. METRICS HUB → write `dashboard/metrics-hub.md` + create the join layer (Google Sheet hub, or
   warehouse if available). Every field maps back to `metrics/aarrr.md`. No metric exists here without
   a definition there. The hub computes the NSM and its AARRR levers as first-class fields.
2. PER-PERSONA VIEWS → write `dashboard/spec.md`, one view each, fed by the matching `cos-<role>` rollup
   (every view leads with the leader's NSM lever):
   - CEO: **NSM (headline)** · AARRR funnel (the levers) · runway (finance-lens)
   - Product: its lever(s) — activation/retention · time-to-value · feature adoption
   - Marketing: its lever — acquisition by channel · CAC (finance-lens) · experiments (link §12.1)
   - Sales: its lever — pipeline · conversion · win rate
   - CS: its lever — retention/churn/expansion · risk
   - Finance/Analytics: unit economics · burn · runway · the metric dictionary itself
   - Board: read-only snapshot of the CEO/NSM view, exported on cadence (Slides/PDF)
   - (If Persona-Value Matrix §12.4 adopted: add a persona slice to the funnel/retention views.)
3. IMPLEMENTATION → pick one, record it + the rationale in `dashboard/spec.md`:
   - **A. BI tool (fastest)** — Looker Studio (native GA4 + Sheets + CRM connectors) reading the hub.
     Choose when speed and native connectors matter most; lowest build/maintenance.
   - **B. Custom app (most control)** — a purpose-built web app (e.g. Next.js/React) that reads the
     metrics hub + source APIs and renders **role-based per-persona routes**, with auth via Google
     Workspace SSO, deployed on the org's existing hosting and versioned in `org-os`/the eng workflow.
     More effort than a BI tool, but: full control of UX and bespoke views a BI tool can't express,
     ability to **embed narrative + each CoS agent's commentary alongside the numbers**, a board-preso
     mode, no per-seat BI cost, and it becomes a reusable internal product surface. Choose when the
     dashboard is itself a product/operating surface, when persona views are too bespoke for a BI tool,
     or when you want the agent rollups rendered inline.
   - (Either way the data contract is the same: read from the hub, definitions stay in `metrics/aarrr.md`.)
4. VALIDATION GATE → analytics-lens signs off every metric definition; finance-lens signs off every
   money number; leadership reviews each persona view — BEFORE anything is shared.

# OUTPUTS
`dashboard/metrics-hub.md` · `dashboard/spec.md` · the live dashboard · the board export.

# GUARDRAILS
CRM/analytics access via 1P service accounts, never human tokens. No client-confidential rows in a
view that sits on a personal-account surface (§9.1). Refresh cadence + owner recorded in the spec.
````

## Appendix D — Worked example (kept separate to stay lean)

A full worked example — a fictional **"Helio"** `org-os` showing the Org North Star (§1.1) cascading through `north-star.md`, `metrics/aarrr.md`, the two adjudicators, a CoS spec, the red-team agent, the generated onboarding skill, and the dashboard build-kickoff — is maintained as a **separate companion sample** rather than inline, so this playbook stays readable. The fastest way to see it for *your* company is to run Part II against your own `org-os`.

---

## Authors, license & attribution

### Authors
- **Michael Taus** — author & editor.
  LinkedIn: https://www.linkedin.com/in/mtaus/ · Website: https://www.michaeltaus.com/
- **Claude (Anthropic)** — drafting, structure, and iteration, in collaboration with the author.

### License
This work is **dual-licensed** so the prose and the code/templates each get the right terms:

- **Documentation & prose** — everything *except* the fenced code/config/skill blocks — is licensed under
  **Creative Commons Attribution 4.0 International (CC BY 4.0)**: https://creativecommons.org/licenses/by/4.0/
  You may copy, redistribute, remix, and adapt it for any purpose, including commercially, **with attribution**.
- **Code, configuration & templates** — the fenced snippets (agent configs, the onboarding skill, the
  dashboard kickoff, YAML/metadata blocks) — are licensed under the **MIT License** (below). Lift them into
  your own software freely.

Why dual rather than MIT-only: MIT is a software license (its text governs "the Software") and is an awkward
fit for written guidance; CC BY 4.0 is the standard for documents. Dual-licensing gives reusers clean,
correct terms for whichever part they're using. _(If you prefer one license for everything, CC BY 4.0 alone
is the simplest defensible choice; choose CC BY-SA 4.0 instead if you want derivatives to stay open under the
same terms.)_

### Required attribution notice
When you reuse or adapt this material (in whole or in part), retain a credit such as:

> *"AI-Native Startup OS — Master Runbook & Playbook"* by **Michael Taus** with **Claude (Anthropic)** —
> licensed CC BY 4.0 (documentation) / MIT (code). Source: https://www.michaeltaus.com/ai-native-startup-os/

### MIT License (applies to the code/templates only)
```
MIT License

Copyright (c) 2026 Michael Taus

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
