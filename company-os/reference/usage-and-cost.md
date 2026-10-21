# Budget the allowance, not just the dollars

`lens-roster.md` says a full panel is a serious spend. This is the other half: what actually runs
out, what spent it, and what moved it.

**Where this comes from, and how far to trust it.** One operator on a flat-rate plan, about a dozen
parallel sessions, a few weeks of watching the meter. The *ordering* of causes in § 2 is from one
deployment and one bad week — treat it as a place to start measuring, not a law. Each lever says what
is observed (`DID`) and what is only standard practice we haven't measured (`SAID`).

---

## 1. On a flat-rate plan the scarce thing is the window, not the dollar

Flat-rate plans meter use in windows — a rolling short window, a weekly cap across all models, and
sometimes a separate weekly cap on the top model tier. Hitting the weekly cap stops **every** session
at once, however well each one is behaving. Dollars only matter if you are paying per token, and then
the same levers apply.

**Read the pace, don't recall it.** Percent used against hours elapsed in the window, weekly. One
week ran at 30% used after about a day of a seven-day window — nearly twice the sustainable pace, and
on course to run out around midweek. The coordinator (`multi-session.md` § 3) posts the pace and the
projected exhaustion date, because by the time every session stops it is too late to choose what
stops.

---

## 2. What spent it, in the order we found it

1. **Context size × turns.** Every turn in a long-lived session re-reads its whole context. A session
   at 0.9 million tokens costs far more per message than a fresh one, and does it for every wake.
   (`DID` — observed; sizes ranged from about 0.3 to 0.96 million.)
2. **Model tier per role.** Work that needs a top-tier model is architecture, security review and
   adversarial challenge. Most other work does not. A role left on the default tier by accident is the
   cheapest saving available. (`DID` — this harness already encodes it in agent frontmatter, recorded
   in `PROFILE.md` § 10: the seats that need depth are set higher and the rest are not.)
3. **The number of wakes.** Messages and scheduled ticks each start a turn (`multi-session.md` § 5).
4. **Scheduled runs.** The scheduler we used had no per-task model setting, so a scheduled task ran on
   the default unless it delegated. (`DID`)
5. **CI minutes** — a separate meter in a different currency. (`SAID`: standard practice; our trim was
   made but the saving wasn't measured.)

---

## 3. Levers

**Context.**

- Treat each session's context as a budget. **Compact or restart before about 85%,** and hand over
  through a written state file, not through the chat.
- A session that has finished its lane should be closed, not left idle in a state where one stray
  message wakes the whole thing.

**Model per role.**

- Default to mid-tier. Pay for the top tier where reasoning is what's being bought.
- **Sub-agents get the cheaper model unless the task needs deep reasoning.** Don't pass a model for
  the sake of it; let the agent definition decide.
- For a scheduled task that needs a model but not a clever one, use a **thin launcher** that hands
  the work to a cheaper sub-agent, so the expensive default isn't the thing doing the reading.

**No-model checks are scripts.** "Is the deploy live? Did the file change? Is the queue empty?" needs
no model. Run it under the operating system's scheduler, and wake a model only when the script finds
something (`operating-patterns.md` § 14).

**Messages and ticks.** See `multi-session.md` § 5. Prefer a long fallback heartbeat to a short poll,
and prefer waiting on an event to either.

**CI.** Path filters so a docs-only change skips the suite; cancel superseded runs on the same branch;
a small smoke set at the draft stage, the full suite when the pull request is ready; and put every
scheduled workflow in the registry with what it costs. (`SAID`)

---

## 4. A spend threshold is a delegation, so write it as one

Asking the operator before every small model call recreates the bottleneck from
`operating-patterns.md` § 9. The same mechanism applies, as a **capped, named slice**:

> Under $N of estimated model spend per task, proceed with the cheapest adequate model. Over it, ask.
> This covers spend only — never data, keys, or anything sent externally.

Record it in `PROFILE.md` § 8 in the operator's own words with date and place, log each use with its
estimate, and note that the cap is the operator's to move. A cap an agent raises on its own is not a
cap.

---

## 5. What to measure

Per week, and write it down: allowance used against hours elapsed; peak context per session; wakes
per session per day; CI minutes per workflow. Without those four there is no way to tell which lever
worked, and the next overrun gets diagnosed from memory (`operating-patterns.md` § 12).
