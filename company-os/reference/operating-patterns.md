# Operating patterns

Patterns learned running this harness on a real company. Each one is here because the obvious
approach failed in a specific, repeatable way. None of them are theory.

Several are **options, not defaults.** Where that's true it says so, and names the failure mode of
each setting. Changing a default is the operator's call, never an agent's.

---

## 1. The approval gate has to be cheap, and it has to stay human

**The failure.** A strict "no agent ever merges" rule sounds like the safe choice. In practice it
makes the operator the click-bottleneck: work finishes, then waits on one person to open each item
and press a button. The queue stalls behind the one human, and the system's speed advantage
evaporates.

**The pattern.** Separate *judgment* from *mechanics*. The operator still decides; the agent performs
the merge under a gate that can refuse.

`PROFILE.md` § 6 records which posture this profile uses:

| `merge_authority` | What happens | Failure mode to accept |
|---|---|---|
| **`operator`** *(default)* | Only the operator merges. Agents prepare and report. | The operator is the bottleneck. Throughput caps at their attention. |
| **`agent-on-label`** | The operator applies an approval label; an agent merges through the gate below. | The label becomes the whole gate. If it can be applied loosely, the gate is loose. |

### If you choose `agent-on-label`, the gate must refuse all of these

A merge proceeds **only** when every one holds. Any failure, or any check that errors, stops the
merge — **fail closed, never fail open:**

1. **The branch is not behind the base.** Not "was current when CI ran" — current *now*.
2. **CI passed on this exact head commit.** A pass on an earlier commit is not a pass on this one.
3. **The approval is newer than the head commit.** This one is easy to miss: operations that update a
   branch carry existing approvals forward onto new commits. Compare the approval's timestamp against
   the head commit's timestamp and reject anything approved before the code it's approving.
4. **A production health probe passes**, immediately before each merge.
5. **One merge at a time.** Each merge makes every other open branch stale, so a parallel train is a
   queue of invalid greens.

### The caveat that carries the whole thing

**An agent can apply a label.** So the norm — *an agent applies the approval label only on the
operator's explicit say-so, never on its own judgment* — is load-bearing. Without it, this is a
system that approves its own work through a mechanism that looks like oversight.

Write that norm down where the agents read it. A gate whose key is lying on the floor beside it is
not a gate.

---

## 2. Compliance posture: veto or advisory

**The tension.** A compliance lens that can block is the right default — under-declaring exposure is
the common failure, and a lens that only advises gets ignored precisely when it matters.

But in practice a blocking lens tends to enforce *self-imposed* internal rules with the same force as
actual law. Teams write an aspirational policy, the lens treats it as a hard line, and work stops on
something nobody is legally or contractually required to do. The lens becomes an obstacle rather than
a safeguard, and the operator starts overriding it reflexively — which destroys the signal for the
cases that are real.

**The pattern.** Make the posture explicit in `PROFILE.md` § 9:

| `compliance_posture` | What blocks | Failure mode to accept |
|---|---|---|
| **`veto`** *(default)* | Anything the lens does not clear on the sensitive surface. | Self-imposed policy gets enforced as law. Reflexive overrides erode the signal. |
| **`advisory`** | Only a short, explicit list of hard lines. Everything else is a strong recommendation, recorded and visible. | A real exposure gets noted and shipped past anyway. |

**If you choose `advisory`, you must write the hard-line list.** An advisory posture with no hard
lines is not a posture — it's an off switch. The list belongs in § 9, it should be short enough to
remember, and it should contain only things that are actually illegal or contractually binding, not
things that are merely unwise.

Under either posture the lens still runs its own criteria test and still never accepts "this doesn't
touch anything sensitive" as an input.

---

## 3. Stacked branches: don't delete what something else is standing on

**The failure.** Merging with automatic branch deletion removes the parent branch. Any open pull
request targeting that branch is then auto-closed by the host, silently, taking its review history
with it.

**The pattern.** Before deleting any branch after a merge, look up whether another open pull request
targets it. If one does, **keep the branch.** If the lookup itself fails — API error, rate limit,
ambiguous response — **keep the branch.** The cost of an extra stale branch is nothing; the cost of a
silently closed PR is a lost review.

Fail closed. This is the same rule as the merge gate: an errored check is a failed check.

---

## 4. Hand-offs arrive late — so carry the measurement, not the conclusion

**The failure.** A message from one session to another lands behind whatever the receiver is already
doing. It can arrive minutes later, after the receiver has already acted. Instructions based on a
state read at 10:00 arrive at 10:20 and are applied to a world that moved.

**The pattern.** Every hand-off states **what was measured, what it was measured against, and when:**

> "As of commit `abc1234`, PR #41, measured 14:05 — three checks failing."

Not: *"three checks are failing, fix them."*

Two rules follow:

- **The later measurement wins.** If the receiver's own reading disagrees with the one in the
  message, the receiver's reading is the current one. The message is evidence about the past.
- **Re-read before acting on a received instruction.** The hand-off tells you where to look, not what
  you'll find.

---

## 5. A check counts only once you've seen it fail

**The failure.** A guard that has never gone red is not a guard. It's a green light wired to nothing,
and it is worse than no guard at all because it's generating false assurance. The same applies to a
sweep that finds nothing: an empty result and an all-clear result look identical in the output and
mean completely different things.

**The pattern.**

- **Mutation-test every guard.** Deliberately break the thing the check protects and confirm the
  check goes red. If it stays green with the fix removed, it proved nothing. Do this when you write
  it, and note that you did.
- **Assert that the probe found something.** A check should fail loudly when its own input set is
  empty, rather than passing. "Scanned 0 files, all clean" must not read as success.
- **State which checks have been seen failing** when reporting that something is verified.

This is the operational form of the evidence grading in `evidence-grades.md`: a passing check you've
never seen fail is a `SAID`-grade claim about your own code.

---

## 6. Masked-output tools, for review without exposure

**The problem.** Sometimes something has to be reviewed, diagnosed, or counted, but the underlying
data must not enter an agent's context at all — not quoted, not summarized, not paraphrased.

**The pattern.** Build the tool so its *output cannot carry the sensitive token.* Not a rule telling
the agent not to repeat it — a tool that structurally cannot emit it. Counts, categories, hashes,
positions, pass/fail, "three records matched pattern B" — never the record.

This lets the review happen at full fidelity on the shape of the problem while the data stays
outside the loop entirely. Record which tools have this property in `PROFILE.md` § 9, because the
distinction is invisible at the call site and an agent will otherwise assume the ordinary tool is
fine.

---

## 7. Say what isn't automated — and say what nobody reads

The harness already requires honesty about automation: a cadence that needs a human to start it must
say so, because a gate the operator believes is automatic is worse than no gate.

**Extend that to alert volume.** An alarm that fires constantly stops being read. A real failure
notification fired daily for over two weeks without anyone looking at it, because it sat among dozens
of routine messages that were always safe to ignore. The monitoring worked perfectly. The reading of
it did not.

So when a skill reports on monitoring or checks:

- **Say how often a given alert fires when nothing is wrong.** An alert with a high routine rate is
  a disabled alert, whatever its configuration says.
- **Distinguish "this fired" from "someone saw it."** Only the second one is detection.
- **Treat a long-running unacknowledged failure as two findings:** the failure, and the fact that
  nobody noticed. The second is usually the more serious one.

---

## The thread running through all seven

**The parts of a system that refuse things get used and earn trust. The parts that only produce
documents drift.**

Every pattern above is a refusal: a gate that won't merge, a lens that won't clear, a delete that
won't run, a check that won't pass on an empty set. Those get exercised daily and stay honest,
because when they're wrong you find out immediately.

Skills whose output is a well-structured document have no such feedback. Nobody decides to stop
opening them; they just quietly stop being worth opening. If you extend this harness, **build the
parts that say no first.**
