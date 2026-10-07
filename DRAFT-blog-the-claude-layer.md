# The Claude Layer: Putting an AI in Charge of Your GTM Process

*Draft — not committed. Companion to [Early-Stage Go-to-Market & Scale](https://www.michaeltaus.com/gtm/).*

---

Most founders using AI for go-to-market are using it as a faster writer. Ad copy, cold emails, a
landing page headline in four variations. That works, and it is the smallest possible win.

The leverage isn't in the drafting. It's in the **process** — the test-measure-learn loop that
actually produces product–market fit. In my last post I argued that go-to-market is not a plan but an
iterative learning engine. The obvious follow-on is the question I kept getting after that session:
*fine, but who runs the engine when you're a team of one?*

This post is my answer. I built the thing, I've been running it on my own company, it's free, and the
most useful part of this post is the section where I tell you what doesn't work.

## The Problem With Asking an AI About Your Company

Ask Claude for a go-to-market recommendation and you'll get something articulate and generic. Ask it
twice and you'll get two different answers. Neither knows your activation metric, your buyer, or that
you tried the same channel in March and it didn't work.

The usual fix is to paste context into the prompt. That fails in a specific and predictable way:
**prompts that recite facts go stale.** You write "our activation rate is 22%" into a prompt in
January, and by April the AI argues confidently from a number that stopped being true six weeks ago.
It doesn't know it's wrong. It sounds exactly as certain as it did when it was right.

So the whole system rests on one rule, repeated in every file:

> **Figures: read, don't recall.** Never state a current number from memory. Open the file and read
> it. If the file contradicts what you remember, **the file wins.**

Everything else follows from that.

## What It Actually Is

The **AI-Native Startup OS** is an operating harness for Claude Code. It ships with **zero company
data**. The first time you run it, it interviews you — your buyer, your north star metric, your
channels, where your numbers live, what Claude is not allowed to do — and writes that into one
configuration file. Every agent reads that file. None hardcode a single fact about you.

- **One PROFILE file** holding your company's facts, and nothing else holding them
- **Sixteen lenses** — product, growth, brand, sales, CS, finance, ops, UX, design, security,
  compliance, analytics, comms, architect, plus your customer and a red-teamer
- **Two makers** that draft copy and design — judged by the lenses, never by themselves
- **Twelve skills** for the operating rhythm: setup, intake, triage, blockers, sprint planning and
  review, GTM, the full panel, code review, QA, PR queue, state sweep
- **Graded sources** — every figure traced to a file and marked A through D for trustworthiness
- **Propose-only** — nothing publishes, spends, merges, or deploys without you

## The Panel Is the Point

`/agent-panel` convenes every lens on a decision. `/gtm` convenes just the commercial seats, which is
cheaper and better aimed for anything customer-facing.

Three design choices make it worth more than one smart assistant:

**Every judgment lens declares its own bias.** The finance lens says, in its own instructions, that
it will be conservative and you should discount it when the downside is capped. That one sentence is
what stops an archetype becoming a caricature. A finance agent that only ever says no is one you
learn to skip.

**The red-teamer is one-sided on purpose.** Its job is to argue you're wrong. Make it balanced and
the panel becomes several flavours of agreement — expensive reassurance.

**The customer lens refuses to run without real customer quotes.** No transcripts, no tickets, no
call notes? It declines and says why. An invented composite customer produces confident fiction that
reads exactly like evidence, and you can't tell the difference three months later.

And the rule that matters most: **if every lens agrees, something is wrong.** Either the question was
too easy to be worth the spend, or they've collapsed into one voice. Disagreement is the product.

## What Actually Happened When I Ran It

Here's the clearest thing it's done for me.

A multi-week build was planned and approved: give the AI advisor more context and memory, because it
appeared to be forgetting what users told it. Reasonable diagnosis. Expensive fix.

The plan had a **stop condition written into it before anyone started** — run a small spike first,
and if fewer than about 60% of the observed errors were fixed by better context, stop and re-plan.

The spike fixed zero out of five. The real causes were entirely different: a tool that couldn't save
a preference, interface cards that didn't match what the assistant had just said, and the assistant
answering a question nobody asked. A second session read through real conversations and found no
forgetting at all. The whole build was re-sequenced within hours toward the failures users were
actually hitting.

**The thing that made that work wasn't the AI. It was that the stop condition existed on paper before
anyone was invested in the answer.** Stopping was following the plan rather than losing an argument.
That's a technique you can steal today without installing anything.

### What gets used, and what quietly dies

This is the finding I didn't expect, and it's a partial indictment of my own design:

> **The parts that refuse things get used and earn trust. The parts that only produce documents
> drift.**

In daily use: the review panel before pull requests, an automated reviewer, a gate that holds
anything touching money, authentication or minors until I personally sign off, and a merge script
that refuses stale green checks. Those run constantly.

Several of the propose-only planning verbs — the ones whose output is a nicely structured document —
see far less use. Nobody decided to abandon them. They just stopped being worth opening.

If you build something like this, **build the parts that say no first.** The parts that produce
artifacts feel more impressive in a demo and are the first thing to go stale in practice.

### The human gate has to be cheap

The approval workflow that stuck is embarrassingly simple: I approve by putting a label on a pull
request, and a session merges it — checking freshness, CI, and a production health probe before each
one. One day that cleared about twenty merges without me clicking merge once.

Two lessons in that:

- **If the gate is expensive, you become the bottleneck** and the whole system stalls behind you.
- **The gate has to stay human.** The system can apply labels. A gate that trusted labels alone
  would have been a system approving its own work.

## Where It Breaks Down

A post that only lists the good parts is exactly the failure this system exists to prevent. So:

**The always-loaded instruction file gets believed, not re-checked.** This is the sharpest one. A
file that loads into every session is read as fact. Stale lines in mine caused confidently wrong
actions more than once — claiming a feature was unbuilt when it was live. Treat that file as a
hypothesis, and keep it short.

**Alarms that fire constantly stop being read.** A post-deploy check failed and emailed every single
day for weeks before anyone looked, because it sat among dozens of routine notifications. An alert
nobody reads is worse than no alert — you think you have monitoring.

**Parallel agents produce plausible-looking duplicate and conflicting work.** Unless file ownership
is explicit and someone owns sequencing, you get two confident agents editing the same thing. Messages
between sessions also cross in flight: three times in one day, an instruction arrived after the
receiver had already done the work.

**"Green" is a claim about the base it ran on, not about main.** Every merge makes every other open
branch stale. A serial queue churns.

**It costs real money.** A full sixteen-lens panel is sixteen separate model calls at meaningful
reasoning effort. Use the narrower GTM panel for commercial questions and save the full one for
decisions that deserve it.

**It's worth roughly 20% of the value.** The other 80% is the substrate — real customer quotes,
instrumented metrics, the recurring failure patterns your red-teamer should know cold. Install this
into a company with no instrumentation and you get sixteen articulate agents with nothing to reason
about.

**Nothing runs on a timer.** Every rhythm needs you to start it. I'm saying that plainly because a
cadence you believe is automatic and isn't is worse than no cadence — you stop checking.

## On Evidence Grades

Every claim that drives a decision gets a grade:

| Grade | Means | Example |
|---|---|---|
| **SAID** | Someone told you | "I'd definitely use that" |
| **DID** | Someone behaved | Completed the flow, came back on day 7 |
| **PAID** | Someone gave up something costly | Money, a contract, a migration, a public reference |
| **STUCK** | They'd be hurt if it vanished | Renewed without negotiating, built a workaround on you |

The rule: **never plan on SAID alone.** Twenty interviews saying "I'd buy that" is still SAID —
counting it twenty times doesn't upgrade it. One signed purchase order is PAID and outranks all
twenty on whether to build the thing.

In honesty: I can't yet point to a moment where those four labels specifically stopped a plan. What I
can point to is the underlying habit, which has stopped several things that looked finished and
weren't — sessions routinely retracting their own claims. *"My count was wrong, here's why." "The
check I ran was adjacent to the one that mattered." "My test passed with the fix removed, so it
proved nothing."*

That last one is a discipline worth stealing on its own: **a check only counts once you've seen it
fail.** If you've never watched your test go red, you don't have a test — you have a green light
wired to nothing.

## The Solo-Founder Case Is Not What I Expected

I built this thinking the pitch was "a cross-functional team you can't afford to hire." That's part
of it, but it's not the main thing.

The real value is that **my attention gets spent only on decisions that are genuinely mine.** Product
calls — how a caption should read, what a closed partner's page should say, where to land on a
privacy posture — arrive as one click with the options and a recommendation already laid out. The
engineering decisions get owned outright by the sessions, and I don't see them.

When that boundary slips in either direction, the value drops fast. Agents asking me to adjudicate
technical trade-offs is a waste of the one thing I can't buy more of. Agents quietly deciding product
is worse.

## Try It

Two commands in any Claude Code session, then a third to start the interview:

```
/plugin marketplace add miketaus/ai-native-startup-os
/plugin install company-os
/company-os:setup
```

Setup detects what it can from your repo first, then asks only about genuine gaps. When you don't
know an answer, say so — it records `TBD — open question` and reports it back rather than inventing
something. That's the ethos in one behaviour.

Open source: documentation under CC BY, code under MIT.
[github.com/miketaus/ai-native-startup-os](https://github.com/miketaus/ai-native-startup-os)

## One Honest Caveat

This doesn't do your customer discovery for you. It cannot. It tightens the loop *around* discovery —
grading what you learned, pressure-testing what you concluded, keeping your persona–value matrix
alive instead of letting it die in a slide from a workshop six months ago.

You still have to get out of the building and talk to people. Everything in the last post still
applies. This just means you don't have to be the only one in the room when you get back.

Startups are hard. Don't go it alone.

---

*Questions, or you built something better? [Find me on LinkedIn](https://www.linkedin.com/in/mtaus/).*
