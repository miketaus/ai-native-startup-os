# Evidence grades

Four grades, weakest to strongest. Every claim that drives a decision gets one, stated out loud.

| Grade | Means | Typical source | Weight |
|---|---|---|---|
| **`SAID`** | Someone told us. | Interview, survey, "I'd definitely use that", a sales call, an advisor's opinion, a competitor's marketing page | Weakest. Directional at best. |
| **`DID`** | Someone behaved. | Used the feature, completed the flow, came back on day 7, opened the email, clicked, filed the ticket | Real, but cheap actions prove little. |
| **`PAID`** | Someone gave up something costly. | Money changed hands · signed a contract · issued a PO · migrated their data · sent their team to training · gave a public reference | Strong. Costly signals are honest signals. |
| **`STUCK`** | They'd be hurt if it went away. | Retention through a price rise · a workaround built on top of us · an escalation during an outage · renewal without negotiation · depends on it operationally | Strongest available. |

## The rules

1. **State the grade with the claim.** *"Three prospects want SSO (`SAID`)"* — not *"prospects want
   SSO."* The grade is part of the fact.
2. **Never plan on `SAID` alone.** A cycle, a launch, a spend, or a roadmap change resting only on
   what people said is rejected — say so, and name the cheapest experiment that would move it to
   `DID` or `PAID`.
3. **Grade the weakest link.** A conclusion is only as strong as the weakest evidence it depends on.
4. **Don't launder a grade.** Counting how many people `SAID` something does not make it `DID`.
   Twenty interviews is still `SAID`. A waitlist signup is `DID` for *interest*, not for *the product*.
5. **`PAID` beats volume.** One paying customer outranks fifty enthusiastic non-buyers on whether
   something is worth building. It does not outrank them on whether the market is large.
6. **Name what would upgrade the grade.** Every `SAID` should come with the concrete, cheap thing that
   would make it `DID` or `PAID`. That is usually the most valuable sentence in the analysis.

## Common laundering patterns to catch

| Looks like | Actually is |
|---|---|
| "Users are asking for X" | `SAID` |
| "X was our most-requested feature" | `SAID`, counted |
| "We got 400 waitlist signups" | `DID` — for a landing page, not the product |
| "Engagement is up 30%" | `DID` — check whether the metric proxies value or activity |
| "They said they'd buy at $99" | `SAID`. Pricing intent is the least reliable `SAID` there is. |
| "They signed a LOI" | Between `SAID` and `PAID` — closer to `SAID` if it's non-binding |
| "They renewed" | `PAID`, and edging toward `STUCK` if it renewed without negotiation |
| "Churn is low" | `STUCK` — the strongest thing most companies already have and rarely mine |

## Where the grades come from

Evidence sources and their reliability grade (`A`–`D`) are in `PROFILE.md` § 4. The two grading
systems are different and both matter: **§ 4 grades the source's accuracy; this file grades the claim's
strength.** A `PAID` signal read out of a `D`-grade spreadsheet is still shaky — say both.
