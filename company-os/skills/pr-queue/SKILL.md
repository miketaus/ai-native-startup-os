---
name: pr-queue
description: Report the state of open pull requests — what's mergeable, what's blocked, what's gone stale, and what needs the operator. Use for a standing sweep of review load. For reviewing one change in depth, use review-panel.
---

# pr-queue — what's open, what's blocked, what's mergeable

> **Code profiles only.** `setup` deletes this skill when the profile ships no code.
>
> **Not `review-panel`** — that reviews one change in depth. This reports on the whole queue.

## Before anything

Read `PROFILE.md` § 6 (repos, branch and PR convention, CI, who can merge), § 4 (work tracking),
§ 5 (lanes).

Check access: is `gh` authenticated, or is there another § 6 access path? **If you can't reach the
repo, say so and stop** — a queue report built on guesswork is worse than none.

> **Read, don't recall.** Fetch the current queue in this run. PR state changes hourly; a remembered
> queue is fiction.

## Run it

**1. Fetch every open PR** across the § 6 repos. Author, age, size, CI state, review state, target
branch, linked tracker item.

**2. Sort into what the operator can act on:**

| Bucket | Means |
|---|---|
| **Mergeable now** | CI green, approved, no conflicts. Just needs the merge — which is gated. |
| **Needs review** | Waiting on a human. Name who, and how long they've been waiting. |
| **Needs the author** | Changes requested, CI failing, or conflicts. The ball is theirs. |
| **Blocked externally** | Waiting on a dependency, a decision, or another PR. |
| **Stale** | No movement well beyond normal cycle time. Merge it, or close it — both are decisions. |

**3. Age everything.** A PR open three weeks is a signal about the process, not about the code. Old
PRs get harder to merge every day and are usually abandoned rather than finished.

**4. Flag size.** Very large PRs don't get reviewed properly — they get approved. Say so when you see
one; it's a real finding.

**5. Flag CI failures with the actual error**, not "CI failing." Fetch the failure. Half the time it's
a flake and knowing that changes the priority.

**6. Flag anything untraceable.** A PR with no linked tracker item or no obvious lane — either the
tracker is out of date or the work is unplanned. Both worth knowing.

**7. Flag sensitive-surface PRs.** Anything touching `PROFILE.md` § 9 needs `compliance-lens` before
merge. Say which, and that it hasn't run yet unless it has.

## Output

```
QUEUE: <n> open across <repos> — read <when>
ACCESS: <ok — or "COULD NOT REACH <repo>: <why>">

MERGEABLE NOW — yours to merge
  #<n> <title> — <author> — <age> — <size> — <linked item>

NEEDS REVIEW
  #<n> <title> — waiting on <reviewer> for <duration>

NEEDS THE AUTHOR
  #<n> <title> — <changes requested | CI failing: real error | conflicts>

BLOCKED EXTERNALLY
  #<n> <title> — waiting on <what>

STALE — merge or close, both are decisions
  #<n> <title> — <age>, no movement since <when>

FLAGS
  Oversized: #<n> — <+/- lines> — won't get a real review at this size
  Untracked: #<n> — no linked item
  Sensitive surface: #<n> — touches <what> — compliance-lens has not run

NOT AUTOMATED
  Nothing here merged, closed, commented, or nudged anyone. All of that is yours.

GATED — YOURS TO DO
  <the merges, closes, and nudges this implies>
```

## Rules

- **Fetch, don't remember.** PR state is the most volatile thing in this harness.
- **Real CI errors.** "CI failing" is not a finding; the actual failure is.
- **Age everything, lead with the oldest.** Old PRs are the ones being avoided.
- **Never merge, close, or comment.** All gated to the operator (`PROFILE.md` § 8). Say so every run.
- **Say when the report is partial.** A queue report presented as complete when a repo was unreachable
  is how work goes missing.
