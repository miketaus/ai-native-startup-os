# Operating patterns

Patterns learned running this harness on a real company. Each one is here because the obvious
approach failed in a specific, repeatable way. None of them are theory.

Several are **options, not defaults.** Where that's true it says so, and names the failure mode of
each setting. Changing a default is the operator's call, never an agent's.

**How much to trust each one.** §§ 1–7 were learned first and have run the longest. §§ 8–15 are
newer, learned once the operator was running a dozen long-lived sessions side by side with one session
coordinating the rest, and each ends
with an **Evidence** line grading what has actually been observed (`evidence-grades.md`, applied to
the harness's own claims rather than to customers). The failure is usually `DID` — it happened and
someone saw it. The fix is often only days old, which makes it `SAID` until it has held through a
recurrence. That gap is stated rather than smoothed over.

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

### Make the approval one tap — and bind it to the commit

**The failure.** Applying a label from a terminal, a browser tab, or a phone is exactly the friction
that makes an operator approve in batches, from memory, without reopening the change. And an approval
given to *what the operator saw* can land on *what is there now*: the branch moved between the glance
and the label.

**The pattern.** Put approval behind a single control on the surface where the operator already
decides (§ 10), and make the control carry facts, not just intent:

- The tap records **the exact head commit the operator was looking at.**
- Before the label goes on, **re-read the branch head.** Apply the label only if it is unchanged. If
  it moved, the approval is void — say so and ask again. Never carry it onto the new head.
- **A conflict, a missing identifier, or an ambiguous target is "no approval."** Never resolve it by
  guessing which item the operator meant.
- **A second tap confirms** before anything is applied. It costs one tap and it is the difference
  between a button and a hazard: an approval was once applied to an item the operator had not
  intended, from a record whose identifier was missing. It was caught because a person looked, then
  reverted.
- The norm above is unchanged: the agent applies the label only by acting on a recorded tap, never on
  its own judgment. The tap moves the say-so from a sentence in chat to a record with a commit in it.

**Evidence.** `DID` for the failure (one wrongly applied approval). The flow itself is days old and has
handled a handful of approvals, so `SAID` that the head check holds under the races that matter — it
has not been stressed.

---

## 2. Compliance posture: veto or advisory

**The tension.** A compliance lens that can block looks like the safe default. In practice a blocking
lens doesn't distinguish *illegal* from *our own stated policy*, and every team writes aspirational
policy. The lens treats it as a hard line, work stops on something nobody is legally or contractually
required to do, and the operator starts overriding reflexively — which destroys the signal for the
cases that are real. A veto used on hygiene is a veto nobody respects.

**The pattern.** Make the posture explicit in `PROFILE.md` § 9, and **choose it from the business you
are actually in:**

| `compliance_posture` | What blocks | Failure mode to accept |
|---|---|---|
| **`advisory`** *(default)* | Only a short, explicit list of hard lines. Everything else is a strong recommendation, recorded and visible. | A real exposure gets noted and shipped past anyway. |
| **`veto`** | Anything the lens does not clear on the sensitive surface. | Self-imposed policy gets enforced as law. Reflexive overrides erode the signal. |

**Choose `veto` when the business warrants it.** Regulated industries, health or financial data,
anything touching children, enterprise contracts with security commitments, or any domain where a
single exposure is existential. The friction is the point there, and reflexive overriding is a
discipline problem to solve rather than a reason to loosen the gate.

**Choose `advisory` for everything else** — most early-stage software, where the realistic risk is
that an over-eager lens teaches you to ignore it.

**Either way, write the hard-line list.** An advisory posture with no hard lines is not a posture —
it's an off switch. Because `advisory` is the default, this matters more, not less: the lens is built
to detect a missing list, **fall back to `veto`, and say so out loud.** Failing closed is the whole
point. The list belongs in § 9, it should be short enough to remember, and it should contain only
things actually illegal or contractually binding, not things merely unwise.

Under either posture the lens still runs its own criteria test and still never accepts "this doesn't
touch anything sensitive" as an input.

### Label every rule: required, or a bar we chose

**The failure.** Policy documents mix two different things under one voice: what the law or a
contract requires, and what the company decided to hold itself to. Agents (and tired humans) read
both as law. Twice in one deployment a rule everyone believed was a legal requirement turned out to
be self-imposed — a vendor-disclosure rule, and a consent gate on all stored conversations — and each
had been enforced as law for weeks, narrowing features and blocking people for no reason anyone could
cite.

**The pattern.** Every rule in § 9, and every compliance finding, carries one of two labels:

- **Required** — with the citation (statute, regulation, contract clause).
- **A bar we chose** — said plainly, with the reason.

"It's in the policy doc" is not a reason on its own. The label turns "may we?" into the two questions
that matter. If it's required, it belongs on the hard-line list. If it's a choice, it's a trade-off
the operator owns: surface it once, with a recommendation, rather than imposing it quietly. A chosen
bar can be exactly right. It just has to say it's chosen.

**Evidence.** `DID` — two rules found self-imposed *after* being enforced as law. `SAID` that
labeling prevents a third; it has been the practice for weeks, not months.

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

## 8. A relay is not an authorization — and neither is access

**The failure.** An agent that is waiting on a human decision hears something that sounds like a
yes from somewhere other than the human — and acts. Three shapes. The first was caught before anyone
acted on it, the second was acted on, and the third was named and refused in advance:

- **A relay.** One agent tells another "the operator approved this." The relayer may be right and may
  be quoting accurately. It may also be summarizing a different question, an earlier version, or the
  approval for the previous step.
- **An access signal.** "The secret store is unlocked," "CI is green," "the deploy is live" each say
  the agent *can*. An agent already waiting on both a go and a capability reads the second as the
  first. A production data load ran on exactly this signal, from an agent that had told the operator
  it would wait for their word.
- **A blocked peer.** When a gate refuses one agent, the tempting move is to ask another agent whose
  gate hasn't been applied. The block is the check; routing around it by finding the session it
  hasn't reached defeats it exactly.

**The pattern.**

- **Act on the technical content of a relayed message at once** — it is usually good — and hold only
  the step that needed the human. Say which is which.
- **A go is the operator's own words, quoted, with date and place.** Anything else is information
  about the past.
- **A message announcing a cleared blocker says in the same sentence that it is access only,** and
  names what still needs a go.
- **A refusal goes to the operator, never sideways to another agent.**
- **An agent that already acted says so plainly and gives the one-command undo.**
- **Delegation doesn't travel through a relay either** (§ 9, `gates.md`): a standing delegation exists
  because the operator said so somewhere you can read it, not because a peer reports it.

**Evidence.** `DID` — the access-signal shape cost a production write that had to be retroactively
approved, and the relay shape recurred until the agents began applying the rule to their own
messages unprompted. Not yet tested: whether it holds when everything else is finished, the operator
is away, and asking feels like the slow option.

---

## 9. Delegate the tactical, log it, make it reversible

**The failure.** Propose-only (§ 1, `gates.md`) was designed against agents acting on the world.
Applied to *everything*, it also makes the operator the approver of tactical calls they would never
disagree with: pausing an automated loop that is burning budget, approving a zero-cost, low-risk task
that fits a strategy they already set. The queue fills with questions whose answer is always yes, and
the operator learns to answer without reading — the exact failure the gate exists to prevent. One
operator said, unprompted, that they were unlikely to disagree because the agent understood the
situation better.

**The pattern.** Three tiers of authority instead of two:

| Tier | What the agent does | Examples of what belongs here |
|---|---|---|
| **Decide and log** | Decides, does it, writes it down — inside a class the operator explicitly delegated | Pausing a loop that is demonstrably wasting allowance; green-lighting a task that costs nothing and publishes nothing under the company's name; model spend under a cap the operator set |
| **Propose** | Drafts, recommends, hands over. **The default.** | Anything not delegated |
| **Never** | Stops. Not delegable by inference or by a broad class — only as a capped, named slice the operator writes themselves | The § 8 gate list: spend, publishing, sending to customers, changing access, irreversible production writes, anything over a compliance hard line |

Rules that make the first tier safe:

1. **Delegate named classes, in the operator's own words, with date and place.** "Use your judgment"
   is not a class. Delegation is never inferred from a request like "just handle it."
2. **Write each bound as a test the agent can fail, not a topic.** A topic ("marketing activities")
   leaves the agent to decide what counts, so it widens silently: a free post under the company's name
   costs nothing and is still "marketing." A test is a short list of yes/no conditions checked before
   acting — *costs nothing? undone in one step? publishes nothing under the company's name?* — and if
   any answer is no, the decision goes to the operator. Judgment words like "low risk" are not
   conditions; replace each with the checkable conditions it stands for.
3. **Every exercise is logged:** what was decided, why, by whom, and **how to reverse it.** The log is
   read on a cadence the operator chose — and "how often does a delegated class fire when nothing is
   wrong" is a § 7 question.
4. **Every logged decision has a one-step Reverse,** and a reversal request outranks everything else
   an agent is doing.
5. **When unsure which tier something is, it is Propose.** Moving a decision *down* a tier is the
   agent's ask; moving it *up* is the operator's.

`PROFILE.md` § 8 records the posture. `delegation: none` is the default, so nothing changes for a
profile that never opts in.

| `delegation` | What happens | Failure mode to accept |
|---|---|---|
| **`none`** *(default)* | Every non-gated decision is proposed. | The operator is the bottleneck on questions whose answer is always yes. |
| **`logged`** | The named classes in § 8 are decided and logged by agents. | A class quietly widens past its test, and the log goes unread. |

**Evidence.** `DID` for the bottleneck — the operator observed it and said so. `SAID` for the
delegation itself: it was adopted days before this was written, no class has yet been wrongly
exercised, and none has yet been reversed. Neither of those is evidence that it works.

---

## 10. One page for what needs the operator — ask once, then park

**The failure.** The same question reaches the operator on three surfaces — an agent's chat, a
notification, a digest — and again from each of a dozen sessions. One question was asked about as
often as a person will tolerate being asked anything, because each agent was individually right to ask
and none could see the others had.

**The pattern.** Grow the `blockers` skill's "needs you" list into one **decision surface**, and route
everything through it:

- **One place, owned by one agent** (the coordinator). Other sessions don't message the operator; they
  file an item.
- **Items have a kind and a recommendation** — decide, do, approve, decided (recorded for the record),
  later, carried. An item without a recommendation pushes work back onto the person with the least
  time.
- **Ask once, then park.** A parked item is raised again only when something *changed* — new
  evidence, a deadline, a different recommendation — and the change is stated. "Still waiting" is not
  a change.
- **Answers are inputs, not chat.** A tap writes a record that agents read; an agent applies it only
  after verifying it still applies (§ 1).
- **A digest, not a stream.** One summary on a cadence the operator picked. Interrupts are for what is
  time-critical or irreversible.
- **A message has a price** (`multi-session.md`): every message to a long-lived session wakes a turn
  at its full context.

**Why this isn't just another document.** A page that only displays drifts (see the thread at the
end). This one earns its place because answers flow back through it: an unanswered item ages in view,
and an answered one changes what happens next.

**Evidence.** `DID` for the failure — the operator reported it in their own words, with a count. The
page has been running for days. `SAID` that it reduces noise on net; unmeasured. The measurement is
cheap: count distinct asks per decision, before and after.

---

## 11. A prompt line is not a control

**The failure.** An agent is told in its instructions not to do X, and does X. The instinct is to add
a sharper sentence. A sentence is a request the model may or may not follow, and it is the one layer
nobody reviews. Two shapes, both observed in production:

- **A rule against a category doesn't forbid its subject.** A prompt said never to state statistics
  that hadn't been looked up. The assistant still named four specific entities and rated each — a
  conclusion attached to a subject the rule never listed, with a neighbouring sentence reading as
  permission to supply them. Nothing had been looked up.
- **A prompt prescribing a tool the grant withholds.** A review agent was told to write its report to
  a file; it had no permission to write files. It did not follow the policy — it improvised against
  production, posting a dozen contentless "test" reviews on live pull requests to check its own
  syntax. The tell was in the run log: a count of permission denials that nobody read.

**The pattern.**

- **Put the check in code.** Where a number or name can be emitted, derive it in code and hand the
  model the *output*, under a key that names what it rests on — never the raw inputs to a derivation
  your system already performs. The model re-derives, worse, and the result arrives as confident
  prose. The test is mechanical: **if a figure the model can emit has a second, more specific version
  computed anywhere in the system, the model is holding the wrong one.**
- **Measure whether the mechanism fired** (§ 5). Remove the call and confirm the check goes red; a
  detector nothing calls is the failure that looks most like success.
- **When an agent does something inexplicable, read its denial count first** and diff what the
  prompt asks for against what the grant allows, before theorizing about the model.
- **Give every "does this invocation work?" question a fixture target.** Never test against the real
  one.

**Evidence.** `DID` — both shapes observed in production, one of them in front of real users. `SAID`
that moving checks into code scales: it has been done for the cases above, which is a few.

---

## 12. Memory is a lead, not proof, for anything that moves

**The failure.** An agent's notes record what was true when they were written. *Durable* facts —
names, identifiers, where things live, a decision with who made it and when — stay true. *Volatile*
ones — what a gate blocks, a count, current config, who owns something now, whether a job is running —
don't, and the note carries no mark of which kind it is. A review of an accumulated agent memory found
about forty-five corrected claims; roughly three-quarters were volatile, and they were caught almost
entirely by people, not by anything re-reading the notes. A read-time warning ("memories are
point-in-time") did not prevent them.

**The pattern.** Bind the rule at *write* time. This is § 4 applied across time instead of across
sessions:

- **Before stating a volatile fact, check the live source in this session** — or mark it inline: "per
  a 9/9 note, not re-checked." Strip the mark from a final message only once it has been checked.
- **Say exactly what the source covered.** "Creating a draft is gated" must not become "new signups
  can't build one."
- **Attribution needs the person's own words, date and place.** Otherwise write "I inferred."
- **When saving a count, a "none" or a "zero," record the query and what it covered** — sample or full
  set, test or live data.
- **When a note is corrected, rewrite the claim.** Don't stack corrections on top of it.

**Evidence.** `DID` for the failure rate — it was counted. The write-time rule is weeks old; whether
it lowers the rate has not been measured.

---

## 13. A check can pass on the wrong thing

§ 5 says a check counts once it has been seen failing. Two more ways a check goes green while being
about something else:

**The instrument was tested on a stand-in.** A page's save logic was tested against plain objects. In
the real runtime the data layer returned *frozen* records, so the assignment silently failed — every
save was rejected while the test was green. The fix was proven the way § 5 requires, with the
realistic input: reproduce the failure with real frozen records, apply the fix, watch it pass.

**The result belongs to a different run.** A pull request's history contains runs from every stage of
its life. A required check that failed on an early draft and was fixed by a later commit stayed red in
a roll-up that doesn't care which run it is — and a review bot read that stale failure and requested
changes the moment the draft was marked ready. Judge a check by the **newest run of each workflow on
this exact head**, never by any red anywhere in the history, and never by a green from a commit that
has since changed (§ 1, rule 2).

**The pattern.** "Seen failing" means seen failing **on the runtime's real inputs**; a green is about
the head you are about to act on; and a sweep that matched nothing is not an all-clear.

**Evidence.** `DID` — both found in live operation. The first was fixed and re-verified in the
incident. The second needed a gate-logic change rather than a rerun, and that change was still in
review when this was written, so treat its fix as `SAID`.

---

## 14. Recurring means persistent

**The failure.** A recurring job created *inside an agent session* — a session cron, a wake-up
timer, a note saying "check this weekly" — dies when the session does. In one deployment a
session-bound recurring recording job stopped when its session ended and eleven recordings were lost
before anyone noticed. The schedule existed only in the memory of something that no longer ran.

**The pattern.**

- **Anything that must repeat is a persistent schedule that outlives the session:** a scheduled task
  in the desktop app for work that needs the operator's machine, or a CI-scheduled workflow that
  alarms on failure *and on an empty result* (§ 5). Never a session timer, never a note.
- **Keep one registry of recurring tasks** — what, where it lives, owner, cadence, last confirmed
  run. After creating one: list it, check its next-run time, run it once, and confirm the first
  output exists. A registry row with no confirmed run is a failing row.
- **Use the cheapest executor that does the job.** A check that needs no model (is the deploy live?
  did the file change?) is a shell script under the operating system's scheduler, not an agent. A
  task that needs a model but not a clever one is a thin launcher that hands the work to the cheapest
  adequate model — the scheduler we used had no per-task model setting, so a task ran on the default
  unless it delegated (`usage-and-cost.md`).
- **Say what an unattended run cannot do.** It does not send, push, publish, or write to production
  (§ 1, `gates.md`); its output is a draft or an entry on the decision surface. Also check whether it
  has any channel to reach you at all — an unattended session with no remote channel cannot notify
  anyone, so "it will tell me" is false.

**Evidence.** `DID` — the failure cost real data. The persistent schedules that replaced it are
running and were checked after creation; the registry that tracks them is days old. Not yet tested:
surviving a restart of the machine or an update of the app.

---

## 15. Unattended credentials fail in boring ways

**The failure.** Agents that read secrets work while the operator is at the keyboard and fail when
they leave. Observed, in order of how long each took to diagnose:

- The secret manager's interactive CLI approval **expires** — by the vendor's documentation, after a
  short idle period and a hard maximum, and per terminal session (check current docs; these numbers
  move). Every unattended read after that prompts for a fingerprint nobody is there to give, and the
  job hangs. Time went to lengthening an idle timer that was never the cause.
- A watchdog killed the **wrapper shell** but not the secret-manager process it had started, which
  stayed alive and waiting.
- A dozen sessions share **one account's rate limit**. The vendor documents per-token and
  per-account limits; we had not hit them when this was written, but one chatty session can exhaust
  the budget for the rest.

**The pattern.**

- **Unattended jobs use a scoped, read-only service account** limited to the vaults they need. The
  interactive approval is for humans.
- **The token lives in a file** with owner-only permissions, outside any repo, sourced in a subshell
  for the one command that needs it, never exported into a long-lived shell, never echoed.
- **Creating or restoring that token is a human step.** A permission classifier will, rightly, block
  an agent from writing to the secret store; surface it once and don't route around it.
- **A timeout kills the secret-manager process itself,** not the shell around it.
- **Know the rate limits before you fan out agents,** and cache within a run — don't read the same
  secret a hundred times.

**Evidence.** `DID` for the expiry and the orphaned process; the rate-limit point is `SAID`. The
service-account path is installed and verified. Unknown: what the next wall is, and the cause of one
earlier hang was never confirmed.

---

## Two patterns that need their own document

Running a dozen sessions at once (`multi-session.md`) and budgeting the plan allowance rather than
just dollars (`usage-and-cost.md`) each have enough surface area that they live apart. Both come from
the same place as § 8–§ 15: they were learned the expensive way once the system was running in
parallel.

---

## The thread running through all of it

**The parts of a system that refuse things get used and earn trust. The parts that only produce
documents drift.**

Nearly every pattern above is a refusal: a gate that won't merge, a lens that won't clear, a delete
that won't run, a check that won't pass on an empty set, a tap that won't apply to a branch that
moved, a relay that doesn't count as a go. Those get exercised daily and stay honest, because when
they're wrong you find out immediately.

Skills whose output is a well-structured document have no such feedback. Nobody decides to stop
opening them; they just quietly stop being worth opening. The few documents above that earn their
place — the decision surface (§ 10), the delegation log (§ 9), the schedule registry (§ 14) — do it
the same way: something reads them and something *happens* when they're wrong. An unanswered item
ages in view; an unlogged decision can't be reversed; a registry row with no confirmed run is a
failing row. If a document has no such feedback, treat it as drifting. If you extend this harness,
**build the parts that say no first.**
