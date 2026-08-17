# Pricing Framework for Freelance AI Engineering (v4)

A pricing system for a senior/staff AI engineer selling agents, automations, and product work as fixed-price outcomes.

**What this system is:** cost/risk-informed pricing, constrained by client value and anchored against the client's alternatives. It is not value-based pricing in the strong sense — that would start from willingness-to-pay and use cost only as a floor, which requires WTP discovery a solo operator rarely gets to perform.

Two non-negotiable principles:

1. **No triple-counting.** Each knob covers exactly one thing:

| Knob | Covers | Does NOT cover |
| --- | --- | --- |
| Effort estimate | Expected work | Uncertainty, profit |
| Risk premium | Delivery variance | Business overhead, profit |
| Margin | Contribution after delivery cost | Project risk |

2. **Never fixed-price unresolved uncertainty.** Everything else is calibration.

---

## 1. Economics: derive your required revenue rate

Not a market rate and not, strictly, a cost rate — the revenue per billable hour the business requires. An **economic floor rate**.

```
Annual revenue requirement = owner compensation + taxes + tools/infra
                             + insurance + overhead + profit reserve

Required revenue rate = annual revenue requirement ÷ (working hours × utilization)
```

Utilization for a solo consultant: **50–60%**. Sales, proposals, admin, and bench time are priced in *only because* utilization shrinks the denominator — that is the mechanism.

> $180,000 ÷ (1,800 × 0.55) ≈ **$180/h**

**Local-economics input:** the revenue requirement must be computed *after* your actual tax structure and FX/payout friction for cross-border invoicing. The framework doesn't localize; the input does. Recompute when your tax regime or the rules change.

Consequences: don't also bill sales time to projects; if you do T&M, quote your (higher) market rate.

## 2. Scope: estimate at two points, define done

Estimate each component at **P50** (realistic) and **P90** (if things go badly):

```
Planning estimate = P50 + 0.4 × (P90 − P50)
```

A **planning heuristic**, not a statistical P70. Its real function is forcing you to enumerate what "badly" looks like.

Rules that live here:

- Communication & PM: **15–20%** of technical hours, more for committee clients.
- **Building the acceptance eval set is in-scope paid work** — typically 10–20% of AI project effort. It appears as an estimated line, never as a free client favor.
- Scope isn't just hours: the **assumptions list** and **acceptance criteria** are drafted before the price is final. They are part of the commercial definition of done.

## 3. Uncertainty: score it, check your own spread, decide the model

Score each factor 0–2:

| Factor | 0 | 1 | 2 |
| --- | --- | --- | --- |
| Requirements | Clear | Some ambiguity | Very unclear |
| Technology | Familiar | Some unknowns | Experimental |
| Integrations | Simple | Several | Complex / undocumented |
| AI behavior | Predictable | Moderate | Highly uncertain |
| Client dependency | Low | Medium | High |
| Operational consequence of failure | Low | Medium | High |
| Data / security / compliance exposure | None / public | Internal / proprietary | PII, financial, health, regulated, prod credentials |

| Score (0–14) | Action |
| --- | --- |
| 0–3 | Fixed price, +10% |
| 4–7 | Fixed price, +20% |
| 8–10 | Fixed price, +35%, tight assumptions, narrow scope |
| 11+ | Mandatory discovery or capped T&M before any fixed quote |

**Second trigger — your own revealed uncertainty:** if **P90/P50 > ~1.8 on the core technical components** (development + integrations, not totals diluted by communication hours), treat the project as 11+ regardless of the additive score. You've already told yourself the uncertainty is unresolved; linear premiums cannot cover power-law outcomes. The 1.8 threshold is provisional, same status as every other prior here.

**Weighting prior:** equal weights are a starting assumption, and probably wrong. Expect **data/compliance exposure** and **AI behavior uncertainty** to predict overruns more strongly than client dependency. The first calibration task after 10–20 projects is reweighting these seven factors against your actual overruns. The score is a decision aid, not a probability model.

**Red flags → automatic discovery or walk, regardless of score:**

- Acceptance defined as "we'll know it when we see it"
- No access to production-like data before the build
- Regulated/sensitive data with no DPA or security process in place
- No named decision-maker, or budget undisclosed alongside urgency
- Client insists on assignment of your pre-existing tooling
- Client shopping the spec you'd produce to cheaper implementers

## 4. Discovery as a product

For any 11+ project, ratio-triggered project, or client who can't articulate requirements:

> **Discovery: $3,000–$8,000, 1–2 weeks.**
> Deliverables: technical spec, architecture, evaluation plan with acceptance thresholds, risk register, assumptions list, and a fixed-price quote for the build. 50–100% creditable against the build.

A high-scoring project can absolutely end up fixed-price — *after* discovery resolves the uncertainty. The trigger de-risks; it doesn't forbid.

**Default parameter for cold outbound:** the first 1–2 scoping conversations with an unproven lead are free and booked as customer acquisition cost, not framework failure. Discovery sells readily to referrals and inbound; it sells poorly to strangers. Budget for that asymmetry instead of resenting it.

## 5. Commercial price: margin and the walk-away price

> Risk-adjusted cost ÷ (1 − 0.35) = price

The 35% is **gross margin — contribution after delivery cost — not net profit.** Compensation, overhead, and unbilled time are already inside the rate via utilization. If you catch yourself justifying margin with overhead arguments, you are double-counting section 1. This confusion will recur; re-read this paragraph when it does.

Then set the number the client never sees:

**Walk-away price** — the minimum at which *this specific project* is worth taking. Distinct from the generic minimum engagement. Moves with: opportunity cost, client quality and payment risk, strategic value (portfolio, niche entry, referrals), schedule disruption, realistic expansion probability. It can sit above or below the computed price. Knowing it before the negotiation is what makes "no" cheap to say.

Framing: you are not selling hours. You are selling a defined deliverable under defined acceptance criteria, taking responsibility for delivery risk **within the agreed assumptions**.

## 6. Value: reference range, then the client's real alternative

Estimate conservative first-year value (hours saved × loaded cost, revenue protected, errors reduced, capacity added). Apply the **value-capture reference range**: 10–20% of conservative first-year value. This is a **sanity check, not a ceiling** — willingness-to-pay moves with urgency, switching costs, strategic importance, multi-year compounding, and attribution confidence.

The stronger anchor:

| Client's alternative | Their true cost |
| --- | --- |
| Hire in-house | $150–250k+ salary, 3–6 month ramp, retention risk |
| Dev shop / agency | Often 2–4× your quote, slower, thinner AI depth |
| Cheaper freelancer | Lower sticker, delivery risk they can't evaluate |
| Do nothing | The quantified value, continuing to leak |

The price is accepted or rejected against that table, not your spreadsheet.

Decision rules:

- Reference range above cost-based price → raise toward it or add scope.
- Reference range below cost-based price → **check the alternatives table before shrinking or walking.** A client facing a $200k hire may rationally pay well above 20% of first-year value. If the alternatives don't rescue it either: shrink scope to what the value supports, or walk. Never discount below cost basis to fit low value.

## 7. Packaging and presentation

Three tiers, anchored high — present the expensive option first:

**Scale — $Z (first).** Multiple agents/workflows, advanced integrations, analytics, optimization, ongoing support.

**Production — $Y (the one you expect to sell).** Complete workflow, integrations, monitoring, evaluation suite, deployment, documentation.

**Pilot — $X.** One workflow, limited integrations, basic deployment, short support window. Pilot rules, because pilots are where margins die: **time-boxed**, scope defined by exclusion, **profitable standalone** (never a loss-leader), and priced so Pilot → Production is an upgrade, not a renegotiation.

**How this lands with the client** — the machinery stays behind the curtain:

- Lead with the outcome and their alternative: "This replaces roughly X hours a week of manual work; the comparable in-house hire is $Y." Never present the rate, utilization, risk score, or margin.
- Frame the **assumptions list and eval criteria as collaborative artifacts** — "here's how we'll both know it's working" — not as defensive clauses. The same document that protects you reads, to a good client, as professionalism.
- Tiers convert price negotiation into scope selection. If they push on price, move scope, not margin.
- Don't sound like a risk manager. The risk system is why you can commit to a fixed number confidently; the confidence is what the client should experience.

## 8. Contract protection

1. **Written assumptions** — every broken assumption converts to a change order.
2. **Change orders** — new scope estimated and priced in writing before work starts. Under 2h absorbed; everything else quoted.
3. **Revision caps** — e.g., two rounds per deliverable within scope.
4. **Eval-based acceptance** — "≥90% task completion on the agreed 50-case test set; ≤2% hallucinated-field rate." Test set built with the client during discovery, as paid work. Passing evals = commercial acceptance = final invoice. Post-acceptance drift from distribution shift is retainer territory — the contract says so explicitly.
5. **Inference costs are the client's** — their API keys, or usage billed at cost plus handling.
6. **Deposit 40–50%**, earned as the corresponding work is performed, subject to the contract's cancellation terms. (Commercial phrasing; blanket "non-refundable" isn't universally enforceable. Counsel reviews the actual contract once engagements are material.)
7. **Milestones** over ~4 weeks (40/30/30). Net 15, late fee stated.
8. **IP by category, assignment on full payment:** client pre-existing IP stays theirs; **your pre-existing frameworks, libraries, tooling, and prompts are licensed, never assigned**; third-party components under their own licenses; project-specific deliverables assigned on full payment, not before.
9. **Liability cap** at fees paid.
10. **USD** quotes and invoices; FX and payout mechanics are your cost structure, not the client's business.

## 9. Recurring revenue: sell capacity, check with the percentage

The retainer's pricing unit is **capacity + SLA + responsibility**. The 15–25%-of-implementation heuristic is a sanity check only, and on small builds it fails outright — 20% of a $22k build is ~$370/month against an SLA that includes model migrations. That's a losing contract dressed as recurring revenue.

| Tier | Scope | Price logic |
| --- | --- | --- |
| Monitoring-only | Dashboards, eval tracking, alerting; no fix hours | **Floor ~$500–800/mo** — watching an AI system has fixed costs regardless of build size |
| Standard | Up to 8 h/mo: fixes, minor improvements, model/API migrations; next-business-day response | ≈ 8h × rate ÷ (1 − margin) ≈ **$2,000–2,500/mo** |
| Priority | Up to 16 h/mo, same-day response | Scale accordingly |

Say the justification out loud: models get deprecated, APIs change, prompts drift, eval scores decay under distribution shift. Ongoing engineering, not a support tax. Larger changes are new projects through this same system.

## 10. Minimum engagement — derived honestly

> **Minimum: $5,000 = transaction cost + minimum contribution**

```
Transaction cost: selling + scoping + contracting + invoicing + support
                  ≈ 10–15h × rate ≈ $1,800–2,700
Minimum contribution: opportunity cost, context switching, payment risk,
                      deep-work interruption ≈ $2,000–3,000
```

The contribution component is a policy choice — own it as one, and never let a client negotiate the minimum down to pure transaction cost.

## 11. Calibration: close both loops, respect the sample

Per project: quoted price, P50/P90 vs. actuals, risk score vs. actual problems, **which factors predicted them**, profit, effective hourly rate (profit ÷ total time incl. sales).

Per proposal: sent/won/lost, reason, price vs. cost basis, lead source, invited vs. outbound.

Rules — valid only for a stable lead source, qualified opportunities, and 10–20 data points:

- Effective rate persistently below required revenue rate → fix estimates or rubric weights, not just the rate.
- Win rate >70% on comparable qualified leads → evidence worth **testing** with a 10–20% increase, not proof of underpricing.
- Win rate <30% → investigate qualification and positioning before concluding it's price.
- Reweight the seven risk factors against actual overruns; validate or move the 1.8 ratio threshold.

Sequencing honesty: this system presumes deal flow. At five proposals a year no rate means anything, and the binding constraint is lead generation, not pricing precision. The framework's early job is preventing one failure — anchoring your market position at desperation prices — while the loops mature over years.

---

## The stack in one pass

```
1.  Economics    required revenue rate = revenue requirement ÷ (hours × utilization)
                 computed after local taxes and FX friction
2.  Scope        planning estimate = P50 + 0.4 × (P90 − P50)  [heuristic]
                 eval-set build is paid, in-scope work
3.  Uncertainty  7-factor score → 10–35% premium; 11+ OR P90/P50 > 1.8 (core) OR
                 red flag → discovery / capped T&M
4.  Discovery    $3–8k product, creditable; cold scoping calls = CAC
5.  Commercial   price = risk-adjusted ÷ (1 − gross margin) + walk-away price
6.  Value        capture reference range as sanity check; real anchor = alternatives
7.  Packaging    Scale (anchor, first) / Production / Pilot (time-boxed, profitable);
                 machinery behind the curtain, scope moves — margin doesn't
8.  Contract     assumptions, change orders, evals, deposit, milestones,
                 IP by category, liability cap, client-side inference costs
9.  Recurring    capacity + SLA tiers; $500–800/mo floor; % as check only
10. Minimum      transaction cost + owned contribution component
11. Calibration  delivery loop + proposal loop; reweight factors; test thresholds
```

---

## Appendix: one project, end to end

**Project:** support-email triage agent for a DTC brand — classifies inbound tickets, drafts responses for human approval, updates the helpdesk and Shopify order context. Warm referral.

**Scope (hours):**

| Component | P50 | P90 |
| --- | ---: | ---: |
| Discovery / design | 4 | 6 |
| Architecture | 4 | 6 |
| Development | 24 | 38 |
| Integrations (helpdesk + Shopify) | 10 | 16 |
| Testing + eval-set build | 10 | 16 |
| Deployment | 3 | 5 |
| Communication & PM (~15%) | 8 | 13 |
| **Total** | **63** | **100** |

Core-component ratio: (38+16)/(24+10) ≈ **1.59 < 1.8** → no ratio trigger.
Planning estimate: 63 + 0.4 × 37 ≈ **78h** → 78 × $180 = **$14,000 base**.

**Risk score:** requirements 1, technology 0, integrations 1 (documented APIs), AI behavior 1 (drafting with human-in-loop), client dependency 1, operational consequence 1 (human approves before send), **data exposure 2 (customer PII in emails)** → **7/14 → +20%**.

> $14,000 × 1.20 = **$16,800** → ÷ (1 − 0.35) = **$25,850 → quote $26,000**

**Walk-away:** referral from a good source, strong portfolio fit, expansion likely → **$19,000**.

**Value:** automation absorbs ~60% of triage effort ≈ 60 h/week × $30 loaded × 50 weeks ≈ **$90k/year**. Capture reference range: **$9–18k — below the $26k cost-based price.** Naive value pricing says shrink or walk.

**Alternatives check — this is the step that decides it:** in-house hire ≈ $140k+ plus ramp; agency quote for equivalent scope ≈ $60–80k; cheaper freelancer = risk they can't evaluate on PII-touching customer comms; do nothing = $90k/year continuing. Against that table, **$26k is the client's cheapest competent path.** The reference range was a sanity check; the alternatives set the price. Quote stands.

**Packages:** Scale $45k (adds multilingual, sentiment routing, analytics dashboard) — presented first. **Production $26k** (full triage + drafting + integrations + evals + monitoring). Pilot $12k (triage/classification only, no drafting, 3-week time-box, profitable standalone).

**Assumptions excerpt:** helpdesk API access within 5 business days; ≤6 ticket categories at launch; English only; client provides 500 historical tickets for eval-set construction; human approval remains in the loop for all outbound drafts.

**Acceptance excerpt:** ≥90% correct classification on the agreed 200-ticket test set; ≥75% of drafts approved with minor-or-no edits on a 50-ticket sample; zero PII leakage across category boundaries in the test suite.

**Retainer:** Standard tier, 8 h/mo, $2,200/month — monitoring, prompt/model updates, API migrations. Stated to the client as the drift-management layer, not support.

**Contract flags from this project:** PII → DPA before data access; client's historical tickets remain client IP; the triage prompt library and eval harness are pre-existing licensed tooling.

One walk-through like this per real proposal, filed with actuals afterward, is the calibration loop doing its job.
