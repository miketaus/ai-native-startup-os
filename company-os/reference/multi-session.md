# Running many sessions at once

Read this when one operator is running more than a few long-lived agent sessions in parallel. Below
about three you do not need most of it; "one session, one checkout" (§ 1) applies from the first day.

**Where this comes from, and how far to trust it.** One operator, roughly a dozen pinned sessions
running side by side for about six weeks: a coordinator, a merge-train session, builders per lane,
analysis and marketing lanes, and a QA gate. The collisions in §§ 1–2 are `DID` — they happened and
cost real work. The cost model in § 5 is `SAID`: context sizes were observed, but how much allowance
the messages themselves burned was not measured. Each section says which it is.

---

## 1. One session, one checkout

**The failure.** A git checkout has exactly one current branch and one index. Two sessions in the same
folder switch branches under each other: one session's commit lands on the other's branch, and its
working-tree edits vanish. A font change once landed on an analytics branch this way.

**The pattern.** The rule is mechanical, not a preference. Before your first commit, list the
worktrees and confirm your path is dedicated:

```bash
git worktree list
```

If you are in a shared checkout and another session may be live, make your own first:

```bash
git -C <primary-checkout> fetch origin && git worktree add ../<repo>-<slug> -b <type>/<slug> origin/main
```

Work, commit, push, and open the pull request from **inside** that worktree. After the merge, whoever
owns the primary checkout removes it. Worktrees share one `.git`, so they are cheap; one branch per
worktree, never the same branch twice.

Three corollaries that bit:

- **The stash stack is shared across worktrees.** Never use bare `git stash` / `git stash pop` — you
  can pop another session's changes. Set work aside with a temporary WIP commit instead.
- **Point a review agent at the worktree that holds the change.** One started elsewhere reviews stale
  code and reports on it with full confidence.
- **Don't mutate a tree other agents are reading.** Committing files out from under a reviewer
  invalidates what it already read.

**Symptoms of a shared tree — stop immediately:** changes you didn't make, your changes gone, the
branch switched under you, or pull-request creation reporting "no commits between base and branch"
after you committed.

**Recovery.** The commit is safe in the object store, just on the wrong branch. Find the SHA
(`git reflog`, `git log --all --oneline`), make a clean worktree off the base, cherry-pick the commit,
open the pull request from there — then **tell the other session it has a stranded commit to drop**,
or it resurfaces later as a phantom conflict.

**Also:** every session pushes under the same identity, so a pull request's author field says nothing
about which session owns the branch. Record ownership in the roster (§ 3). Branches that several
sessions push to get force-pushed over; diff before you push.

**Evidence.** `DID` — several collisions, each costing a rebuild of lost work.

---

## 2. Lanes must be disjoint

Each session owns an explicit, non-overlapping set of files or directories. If two lanes would touch
the same file, re-cut them or sequence them. **Shared config, design tokens and root layout files are
nobody's lane by default** — flag before editing one.

A lane can also be a *job*, not only a directory: merging, running the decision surface, owning the
schedule registry. Moving a lane between sessions is a change to the roster, made by whoever owns the
roster, and announced — not something a session does because it happens to be free.

---

## 3. The roster

Keep one table, owned by the coordinator, and read it before assigning work:

| Session | Lane (what it owns) | Must never |
|---|---|---|
| Coordinator | The decision surface (`operating-patterns.md` § 10), cost, the schedule registry, cross-session messages that aren't hand-offs | Merge, build in another lane's files |
| Merge train | Merging, one at a time (`operating-patterns.md` § 1) | Close or delete anything on a peer's word |
| Builder, per lane | One set of files | Touch a file outside its lane |
| QA gate | Verifying the real path | Build — a tester sharing files with builders causes overwrite conflicts |
| Analysis / marketing / research | Reads, drafts, recommends | Publish, spend, send |

Add a column for the model each session runs on if you want one in the same place
(`usage-and-cost.md` § 3) — but record tiers for *agents* only in agent frontmatter, as everywhere
else in this harness.

The coordinator is not a super-builder. A coordinator that starts building in a lane has stopped
coordinating, and nobody notices because it is still busy.

---

## 4. Reaching another session

Your plain output is invisible to every other session. If it wasn't sent, it wasn't said.

- **Use the harness's session-to-session messaging.** It lands as a turn in the target, labelled with
  the sender. Check the target is running first.
- **A teammate you spawned inside a session is a different feature** from a session someone else
  started. Reaching for the first when you meant the second cost a day of silent collisions: two
  sessions force-rebased each other's branches, neither able to reach the other.
- **A sub-agent's messages go out under the parent session's identity.** Brief a sub-agent that it
  must not message other sessions, or a peer will believe the parent said it.
- **If the right tools aren't loaded, search for them** before concluding the feature is missing.

Reach the other session at the **start** of the work, not after the collision.

---

## 5. The price of a message

**The failure.** Each message to a long-lived session wakes a turn that re-reads that session's whole
context. Observed contexts ran from roughly 0.3 to 0.96 million tokens. A one-word acknowledgement
sent to a session at that size is not free — it is a full-context turn spent on "ok."

**The pattern.**

- **No acknowledgement-only messages.** Silence means received. Reply only when the reply changes what
  someone does.
- **Batch.** One message with everything you need from a session, not a drip.
- **One ask per message, stated as a decision with a deadline.** "Please confirm X by Y; default if
  silent is Z" beats an open question.
- **Put state on a shared object and let the receiver read it there** — a label, a file, a board
  column, the decision surface. Don't message to say a shared object changed.
- **A broadcast says "no reply needed"** unless it genuinely needs one, and says whether it changes
  anyone's actions. A cleared-blocker broadcast says it is access only (`operating-patterns.md` § 8).
- **Don't poll by messaging.** If you are waiting, wait on the object, or schedule a check
  (`operating-patterns.md` § 14).
- **Carry the measurement, not the conclusion** (`operating-patterns.md` § 4): sha, number, time.

**Evidence.** `SAID`. The mechanism is certain — a message is a turn and a turn reads the context. The
size of the bill was not measured, and no one has yet compared total allowance burned before and after
these rules.

---

## 6. What to measure

Four numbers are cheap and would tell you whether any of this is working:

1. Collisions per week (stranded commits, branches moved under a session).
2. Messages sent per decision reaching the operator.
3. Peak context size per session, at the end of each day.
4. How long a finished pull request waits for a merge.

None of the four was tracked from the start. Start tracking before you decide the rules above work.
