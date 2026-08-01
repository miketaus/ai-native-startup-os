# Company OS — session bootstrap

> **Copy-install only.** If you installed `company-os` as a plugin, you do not need this file — the
> `setup` skill writes an equivalent stanza into your own `CLAUDE.md`. This file exists for the
> copy-the-directory install path, where it is what makes the harness load automatically.

## First thing, every session

**Read `PROFILE.md` before doing anything else.** It is the only place company-specific facts live.
Every skill and every agent in this harness reads it. None of them hardcode a fact about this company.

Then check whether it is configured:

```bash
grep -c "{{" PROFILE.md
```

- **Prints `0`** → configured. Proceed normally.
- **Prints anything else** → **the harness is unconfigured.** Stop. Run no other skill. Guess nothing
  about this company. Invoke the `setup` skill and let it interview the operator.

A session that starts using `agent-panel`, `sprint-plan`, `triage`, or any other skill while `{{`
placeholders remain is misconfigured. Stop it and run `setup`.

## The rule that everything else depends on

> **Figures: read, don't recall.** Never state a current number — revenue, runway, headcount, pipeline,
> conversion, uptime — from memory or from prompt text. Open the file named in `PROFILE.md` § 4 and
> read it. **If a file contradicts what you remember, the file wins,** and say so out loud.

Prompts that recite facts drift silently out of date and then argue confidently from stale numbers.
This harness is built so that never happens. Do not reintroduce current-state figures into any prompt,
skill, or agent file — including this one.

## Standing constraints

- **Propose-only.** No agent in this harness acts on the world. The gate list in `PROFILE.md` § 8 is
  binding: publishing, spend, merges, deploys, external sends, roadmap and OKR changes belong to the
  operator. Draft them, name them, hand them over.
- **One exception, and it is not an action.** `compliance-lens` may veto a `ship` verdict when a change
  touches the sensitive surface (`PROFILE.md` § 9). It still cannot cause anything to happen.
- **Evidence grades.** `SAID` < `DID` < `PAID` < `STUCK`. Grade the evidence behind any claim that
  drives a decision, and say the grade out loud. Reject planning that rests on `SAID` alone.
- **Say what isn't automated.** If a step needs a human to run it, say so plainly. A gate described as
  automatic when it isn't is worse than no gate, because the operator stops checking.
- **`TBD — open question` is a legitimate answer.** Inventing one is not.

## Where things are

| | |
|---|---|
| Config, and the index of everything else | `PROFILE.md` |
| The sixteen lenses and two makers — what each is for, what each is biased toward | `reference/lens-roster.md` |
| Evidence grading | `reference/evidence-grades.md` |
| Gates and the sensitive surface | `reference/gates.md` |
| The operating model this implements | [`ai-native-startup-os.md`](https://github.com/miketaus/ai-native-startup-os) |
