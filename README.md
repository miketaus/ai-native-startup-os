# gtm-looper

**Design loops, not prompts.** Open-source Claude skills and a publishing pipeline
for founders who'd rather build the machine than crank the handle.

![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)

The most leveraged AI users stopped prompting one instruction at a time and started
writing **loops** — small systems that judge their own work against a metric and keep
going until it's good. This repo gives you two of those loops as installable skills,
plus the pipeline that publishes content built with them.

📄 Start with the essay: [**Write Loops, Not Prompts**](posts/2026-06-11-write-loops-not-prompts.md)

---

## The skills

### 🔁 `loop-design`
The general engine. Turns any task into a loop with three parts — **objective** (what
"done well" means), **metric** (how it scores its own pass without you reading every
word), and **boundary** (what runs unattended vs. what stops for you). Then it shows you
how to add the one feedback wire that turns a loop that *runs* into a loop that *learns*.

### 🎯 `persona-value`
The loop pointed at the only question that decides whether a startup lives: **who values
what?** It maintains one living, company-wide matrix of which buyer personas care about
which value drivers, scored by evidence strength (said → did → paid → stuck), and refines
it from every deal, churn, and experiment. One source of truth for discovery, GTM, and
product scope. Built on the [Persona-Value Matrix](https://www.michaeltaus.com/persona-value-matrix/).

---

## Install

**Claude Code** (available in every project):
```bash
git clone https://github.com/<your-username>/gtm-looper
cp -r gtm-looper/skills/loop-design   ~/.claude/skills/
cp -r gtm-looper/skills/persona-value ~/.claude/skills/
```
Restart Claude Code and run `/skills` to confirm. Invoke directly with `/loop-design`.

**Claude.ai / Desktop / Cowork:** upload the packaged `.skill` files via
**Settings → Capabilities → Skills** (cut a release first with `scripts/repackage_skills.py`).

To make Claude *consult these every session*, also paste the one-line pointer from
[`patterns/loop-design-install-guide.md`](patterns/loop-design-install-guide.md) into
your `CLAUDE.md` / Project instructions.

---

## The idea in 30 seconds

A prompt is a single ask you crank by hand. A loop is the machine that cranks the asks
for you and judges its own output, so your job moves from executing tasks to designing
the system. The keystroke work disappears; the judgment work — picking the objective,
the metric, the boundary — is the part that compounds. Full argument in
[the essay](posts/2026-06-11-write-loops-not-prompts.md).

---

## Build your own publishing pipeline

This repo is also a working content engine: author posts as markdown, publish to Ghost,
amplify to social through a human-approval gate.

- `scripts/publish_to_ghost.py` — push a repo markdown post to Ghost (draft by default,
  idempotent by slug). Set creds in `.env` (see `.env.example`), then:
  ```bash
  python scripts/publish_to_ghost.py posts/your-post.md --dry-run
  ```
- [`BUILD_PLAN.md`](BUILD_PLAN.md) — the full rollout, phase by phase.
- [`CLAUDE.md`](CLAUDE.md) — conventions and guardrails for working in this repo.

---

## Subscribe

New essays on loops, GTM, and building with AI go out to the newsletter first:
**👉 [subscribe here](<your-newsletter-url>)**.

---

## License

MIT — take the skills, fork them, ship them. A link back is appreciated, not required.
