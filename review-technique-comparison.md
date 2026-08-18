# Artifact Review & Refinement Techniques — Comparison, Scoring, and Decision Matrix

Leo Dutra, 2026

**Scope.** Techniques for improving the quality of LLM-produced (or LLM-assisted) artifacts — specifications, architecture documents, code, research answers, business analyses — via review, critique, debate, selection, or verification. Ten techniques are compared. Scores reflect the published literature plus documented failure modes; where a technique has no direct empirical validation, that is scored, not hidden.

**How to read this document.** §1 defines each technique in enough detail to implement it. §2 defines the scoring model. §3 contains the scored matrix and rankings under two weighting profiles. §4 is the situational decision matrix. §5 covers composition rules (which techniques stack). §6 lists failure modes per technique. §7 is the recommendation. §8 lists sources.

---

## 1. The Techniques

### T1 — Sequential Gauntlet Loop

**Shape:** Draft → reviewer critiques → fix → next reviewer (or next round) → repeat until critiques dry up or patience does.
**Mechanism of value:** Iterative depth. Each round sees a strictly better artifact; reviewers can engage with the full document.
**Structural properties:** Rounds are _correlated_ — every reviewer sees a document shaped by prior critiques and anchors on the residue. No principled stopping rule. Freeform critique invites finding-inflation (reviewers Goodhart on producing findings). Disagreements between rounds oscillate rather than resolve.
**Best native fit:** Deep refinement of a single complex artifact where iteration matters more than reviewer independence.

### T2 — LLM Council (Karpathy, 2025)

**Shape:** One pass, three stages. Stage 1: N models answer the same query independently, in parallel. Stage 2: each model ranks all answers, _anonymized_, so it cannot favor itself or a lab. Stage 3: a Chairman model synthesizes a final answer from the responses plus rankings.
**Mechanism of value:** Independence at generation time (no anchoring), comparative ranking (models grade better than they generate), synthesis as a distinct role.
**Structural properties:** Single-pass — there is no revision stage, so it cannot _refine_ anything. Designed for Q&A-sized outputs; holistic ranking of a long document is shallow. Cost is ~2N+1 frontier calls per question. Author's own framing: a weekend hack, no benchmarks in the repo. One independent domain validation exists (clinical reasoning in ophthalmology, 2026, adapted pipeline).
**Best native fit:** One-off hard questions where you want the best single _answer_, not a better draft.

### T3 — Parallel-Blind + Reconcile ("Council-in-Gauntlet" hybrid)

**Shape:** Outer loop = gauntlet (revise and re-run). Inner round = council-style: N independent, mutually blind reviews of the _current version_; a triage step clusters critiques, scores each issue by how many independent reviewers flagged it, and surfaces genuine disagreements as explicit cruxes instead of averaging them away; a fix pass revises against the triaged list. Terminate on a novel-substantive-issue rate threshold (e.g., stop when a round surfaces ≤ 1 new high-severity issue).
**Mechanism of value:** Keeps the gauntlet's iteration and depth; imports the Council's independence and ranking; adds the stopping rule neither parent has. Multi-reviewer agreement becomes a severity signal: issues flagged independently by ≥2 reviewers are almost always real; single-flag issues are coin-flips.
**Structural properties:** Not validated as a named combination (its components are). Costs N reviews per round instead of 1. Triage quality depends on the chairman/triage prompt.
**Best native fit:** Exactly the document/spec refinement workflow the gauntlet serves, minus its two worst flaws (anchoring, churn).

### T4 — Multi-Agent Debate (Du et al., 2023, and descendants)

**Shape:** N agents answer; each then sees the others' answers and revises over multiple rounds; converge or vote. Variants: assigned stances (affirmative/dissenting roles), agreement-ratio modulation (Smit et al., ICML 2024), consensus-free scoring (Free-MAD, 2025), adaptive stopping via consensus-dynamics modeling (NeurIPS 2025).
**Mechanism of value:** Interaction resolves disagreement — agents must respond to specific counterarguments rather than restate positions.
**Structural properties:** Documented "degeneration of thought": once an agent commits to a stance, further rounds add little; herding toward the majority is measurable. Fixes (roles, agreement modulation) materially help. Improves factual/logical accuracy on benchmarks; evidence base is the strongest of the interactive methods.
**Best native fit:** Resolving a _specific contested point_ — not coverage, not document-scale review.

### T5 — Mixture-of-Agents (Wang et al., ICLR 2025)

**Shape:** Layered synthesis. Layer 1: N models draft independently. Layer 2: each model _rewrites_ using all layer-1 drafts as reference material. Repeat for k layers; final aggregator produces the output. No critique step anywhere.
**Mechanism of value:** Models demonstrably produce better outputs when shown other models' attempts ("collaborativeness"). Improvement through synthesis-of-drafts rather than criticism.
**Structural properties:** Beat GPT-4o on AlpacaEval 2.0 using only open models at publication. Synthesis can average away justified strong positions; no mechanism to _detect_ errors, only to dilute them. Best understood as a drafting technique, not a review technique.
**Best native fit:** Generating a strong first draft before any review begins.

### T6 — Best-of-N + Verifier Selection

**Shape:** Sample N candidate outputs; score each with an independent verifier (reward model, rubric grader, execution harness, or multiple diverse verifiers — Multi-Agent Verification, COLM 2025); keep the argmax. This is the pattern production "heavy" modes converged on (parallel sampling + internal selection).
**Mechanism of value:** Exploits generation variance; quality of output bounded by verifier quality rather than generator quality. Fixed compute budget = trivially perfect stopping rule.
**Structural properties:** Strongest empirical track record of anything on this list (test-time-compute scaling literature). Requires a scoring function; collapses when "better" is multidimensional and contested, which is precisely the situation in spec review. Selects; never repairs.
**Best native fit:** Tasks with a checkable or rankable success criterion — code against tests, math against answers, constrained-format outputs.

### T7 — Self-Refine / Reflexion (single-model self-critique)

**Shape:** One model generates, critiques its own output, revises; optionally with a memory of past failures (Reflexion).
**Mechanism of value:** Cheap, zero-infrastructure iteration.
**Structural properties:** The literature is fairly consistent: self-critique without an _external_ signal (tests, tools, a different model) mostly rewrites rather than corrects, and can degrade correct answers. Reflexion's gains come principally from environments that return real feedback, not from introspection.
**Best native fit:** Low-stakes polishing; a free first pass before spending money on independent reviewers.

### T8 — Tool-Augmented Critique (CRITIC pattern)

**Shape:** Reviewers verify claims against external tools — execute the code samples, run the arithmetic, fetch the cited source, query the API version — instead of offering opinions. Critique is grounded in tool output.
**Mechanism of value:** Converts a subset of review from opinion to fact. Fact-grounded critiques do not oscillate, do not herd, and do not inflate.
**Structural properties:** Only covers checkable claims; the judgment-heavy remainder of a spec (scope, framing, audience, trade-offs) is untouched. Composes with every other technique on this list.
**Best native fit:** Any artifact containing claims that can be mechanically checked — which is most technical documents.

### T9 — Role-Pipeline Decomposition (planner → executor → critic → reviser)

**Shape:** Persistent specialized agents in a fixed workflow (MetaGPT/ChatDev/CrewAI lineage), each owning a stage.
**Mechanism of value:** Division of labor; per-stage prompts can be sharper than one generalist prompt.
**Structural properties:** Documented pitfalls: cost explosion (50–100 calls per task is normal), error propagation (a hallucination in an early stage contaminates every later stage), debugging opacity, and over-engineering — most tasks are solvable with one agent, and the field's own postmortem exists ("Why do multi-agent LLM systems fail?", NeurIPS 2025). Role separation adds little _reviewer independence_, which is the property that actually matters.
**Best native fit:** Genuinely decomposable production workflows with per-stage validation — not review of a single artifact.

### T10 — Mechanical / Empirical Verification

**Shape:** Replace opinion with ground truth wherever the artifact admits it: property tests and fuzzing for code, a throwaway spike for the riskiest 10% of a spec, formal checkers (Lean) for proofs, a presale for a business idea, compiling every code sample, checking every citation.
**Mechanism of value:** Reality does not anchor, herd, inflate findings, or prefer verbose answers. One test outweighs any number of review rounds on the slice it covers.
**Structural properties:** Dominant _within its domain of applicability_; the limitation is coverage — most of a spec's value judgments are not mechanically checkable. Setup cost varies from trivial (run the code) to large (formalization).
**Best native fit:** Every checkable slice of every artifact, always, before and instead of opinion-based review of that slice.

---

## 2. Scoring Model

Ten criteria, scored 0–5 per technique. 0 = capability absent; 3 = adequate; 5 = best-in-class.

| #   | Criterion                      | Definition                                                                    |
| --- | ------------------------------ | ----------------------------------------------------------------------------- |
| C1  | **Coverage**                   | Breadth of real issues surfaced in one application of the technique           |
| C2  | **Depth on complex artifacts** | Ability to engage a long, dense, interdependent document                      |
| C3  | **Bias resistance**            | Resistance to anchoring, sycophancy, herding, self-preference, verbosity bias |
| C4  | **Iteration support**          | Native support for revise-and-re-run cycles                                   |
| C5  | **Conflict resolution**        | Ability to _resolve_ (not just record) reviewer disagreement                  |
| C6  | **Stopping discipline**        | Existence of a principled termination condition                               |
| C7  | **Cost efficiency**            | Output quality per API dollar / wall-clock hour                               |
| C8  | **Implementation effort**      | Ease of standing up the technique (5 = trivial)                               |
| C9  | **Empirical evidence**         | Published validation that the technique beats sensible baselines              |
| C10 | **Generality**                 | Applicability across artifact types without a domain-specific harness         |

**Weighting profiles.** Weights are use-case-dependent, so two profiles are computed. Profile A is the primary lens of this document.

| Criterion              | **Profile A: Artifact refinement** (specs, docs, designs) | **Profile B: One-shot answer quality** (hard questions) |
| ---------------------- | --------------------------------------------------------- | ------------------------------------------------------- |
| C1 Coverage            | 0.15                                                      | 0.20                                                    |
| C2 Depth               | 0.15                                                      | 0.05                                                    |
| C3 Bias resistance     | 0.12                                                      | 0.15                                                    |
| C4 Iteration           | 0.12                                                      | 0.00                                                    |
| C5 Conflict resolution | 0.08                                                      | 0.10                                                    |
| C6 Stopping            | 0.08                                                      | 0.05                                                    |
| C7 Cost                | 0.10                                                      | 0.15                                                    |
| C8 Implementation      | 0.05                                                      | 0.05                                                    |
| C9 Evidence            | 0.10                                                      | 0.15                                                    |
| C10 Generality         | 0.05                                                      | 0.10                                                    |

---

## 3. Scores and Rankings

### 3.1 Raw scores (0–5)

| Technique                   | C1 Cov | C2 Depth | C3 Bias | C4 Iter | C5 Conflict | C6 Stop | C7 Cost | C8 Impl | C9 Evid | C10 Gen |
| --------------------------- | ------ | -------- | ------- | ------- | ----------- | ------- | ------- | ------- | ------- | ------- |
| T1 Sequential Gauntlet      | 3      | 5        | 2       | 5       | 1           | 1       | 3       | 5       | 2       | 4       |
| T2 LLM Council              | 4      | 2        | 4       | 0       | 3           | 2       | 2       | 4       | 2       | 3       |
| T3 Parallel-Blind Hybrid    | 5      | 5        | 4       | 5       | 4           | 4       | 2       | 3       | 2       | 4       |
| T4 Multi-Agent Debate       | 3      | 3        | 3       | 4       | 5           | 3       | 2       | 3       | 4       | 4       |
| T5 Mixture-of-Agents        | 2      | 3        | 3       | 3       | 2           | 2       | 2       | 3       | 4       | 3       |
| T6 Best-of-N + Verifier     | 3      | 2        | 4       | 2       | 3           | 5       | 3       | 3       | 5       | 2       |
| T7 Self-Refine / Reflexion  | 2      | 3        | 1       | 4       | 1           | 2       | 5       | 5       | 3       | 4       |
| T8 Tool-Augmented Critique  | 3      | 3        | 5       | 4       | 4           | 3       | 4       | 3       | 4       | 2       |
| T9 Role-Pipeline            | 3      | 3        | 2       | 3       | 2           | 2       | 1       | 2       | 2       | 3       |
| T10 Mechanical Verification | 5      | 5        | 5       | 5       | 5           | 5       | 4       | 2       | 5       | 1       |

**Scoring notes (the non-obvious calls):**

- T2 iteration = 0: the pipeline has no revision stage at all, by construction.
- T3 evidence = 2, not higher: the _combination_ is unvalidated even though every component is; honesty requires scoring the technique as named.
- T6 evidence = 5: the test-time-compute / verifier-selection literature is the most rigorous body of results on this list.
- T7 bias = 1: self-preference is the defining failure of self-critique.
- T10 generality = 1: dominant where applicable, inapplicable to most judgment content. Its C1–C6 scores of 5 are conditional on being within domain — this is the one technique whose row cannot be read unconditionally.

### 3.2 Weighted totals and ranking — Profile A (artifact refinement)

| Rank | Technique                       | Weighted score | One-line verdict                                                 |
| ---- | ------------------------------- | -------------- | ---------------------------------------------------------------- |
| 1    | **T10 Mechanical Verification** | **4.55**       | Unbeatable on the slice it covers; cannot be your only technique |
| 2    | **T3 Parallel-Blind Hybrid**    | **3.97**       | Best complete method for document refinement                     |
| 3    | **T8 Tool-Augmented Critique**  | **3.59**       | The highest-value _modifier_ — bolt onto whatever you run        |
| 4    | **T4 Multi-Agent Debate**       | **3.33**       | Best conflict-resolver; wrong shape as a main loop               |
| 5    | **T6 Best-of-N + Verifier**     | **3.16**       | Wins wherever a scorer exists; specs mostly lack one             |
| 6    | **T1 Sequential Gauntlet**      | **3.15**       | Deep but structurally biased; superseded by T3                   |
| 7    | **T7 Self-Refine / Reflexion**  | **2.84**       | Free polish pass; never load-bearing                             |
| 8    | **T5 Mixture-of-Agents**        | **2.69**       | A drafting technique miscast as review                           |
| 9    | **T2 LLM Council**              | **2.53**       | Great round mechanics, no loop — donate its parts to T3          |
| 10   | **T9 Role-Pipeline**            | **2.37**       | Cost and error propagation without added independence            |

### 3.3 Weighted totals and ranking — Profile B (one-shot answer quality)

| Rank    | Technique                   | Weighted score |
| ------- | --------------------------- | -------------- |
| 1       | T10 Mechanical Verification | 4.30           |
| 2 (tie) | T3 Parallel-Blind Hybrid    | 3.60           |
| 2 (tie) | T8 Tool-Augmented Critique  | 3.60           |
| 4       | T6 Best-of-N + Verifier     | 3.40           |
| 5       | T4 Multi-Agent Debate       | 3.30           |
| 6       | T2 LLM Council              | 3.00           |
| 7       | T7 Self-Refine / Reflexion  | 2.75           |
| 8       | T1 Sequential Gauntlet      | 2.70           |
| 9       | T5 Mixture-of-Agents        | 2.65           |
| 10      | T9 Role-Pipeline            | 2.20           |

**Sensitivity reading.** The ranking is stable at the top (T10, T3, T8 lead under both profiles) and at the bottom (T9 last under both). The interesting movers are T2 (Council) and T6 (Best-of-N), which climb when iteration stops mattering, and T1 (Gauntlet), which falls for the same reason. Conclusion: the choice between Council-like and Gauntlet-like methods is almost entirely a function of whether the artifact persists across rounds.

---

## 4. Decision Matrix

| Situation                                          | Primary technique                          | Secondary / modifier                               | Explicitly avoid                                |
| -------------------------------------------------- | ------------------------------------------ | -------------------------------------------------- | ----------------------------------------------- |
| Refining a spec, architecture doc, or long report  | T3 Parallel-Blind Hybrid                   | T8 on every checkable claim; T4 on split critiques | T1 alone (anchoring); T2 alone (no revision)    |
| One-off hard question, best single answer wanted   | T2 Council or T6 Best-of-N                 | T8 if claims are checkable                         | T1 (nothing to iterate on)                      |
| Code correctness                                   | T10 (tests, property tests, fuzzing)       | T6 with test-pass verifier; review only for design | Opinion-only review of correctness              |
| Code design / API surface                          | T3                                         | T8 (compile the examples)                          | T7 as sole reviewer                             |
| Mathematical or formal claims                      | T10 (checker / formalization)              | T4 for proof-strategy disputes                     | Majority voting of model opinions               |
| Business idea / market claim                       | T10 (presale, customer contact)            | T3 on the written analysis only                    | Any all-LLM loop as validation                  |
| Two reviewers (or rounds) contradict each other    | T4 with assigned stances                   | Human adjudication of the extracted crux           | Another undirected review lap                   |
| Producing a first draft from scratch               | T5 MoA (parallel drafts → merge)           | T7 as a free polish pass                           | Starting the review loop on a single cold draft |
| High-volume grading / evaluation at scale          | T6 with rubric verifier                    | ChatEval-style debate on disagreements only        | T3 (cost)                                       |
| Multi-stage production workflow (not one artifact) | T9, reluctantly, with per-stage validation | T10 gates between stages                           | Free-running pipelines without checkpoints      |

**Tie-breakers when the matrix is ambiguous:**

1. If any slice of the artifact is mechanically checkable → T10 on that slice first, always.
2. If the artifact will be revised → the method must contain a loop (T3, T4, or T1).
3. If "better" is contestable and multidimensional → prefer critique-shaped methods (T3) over selection-shaped ones (T6).
4. If reviewers disagree → escalate to T4; never re-run the same loop hoping for consensus.
5. If the budget is one API call → T7, with expectations set accordingly.

---

## 5. Composition Rules

These techniques are not mutually exclusive; the strongest workflows stack them. Verified-compatible combinations:

1. **T5 → T3 → T10** (draft-merge, then hybrid review loop, with mechanical verification gating each round). The full pipeline for a high-stakes document: MoA produces a stronger cold draft, so the review loop starts closer to done; every checkable claim is tool-verified rather than opined on; the loop terminates on novel-issue rate.
2. **T3 + T4 escalation.** Inside the hybrid, any critique where independent reviewers _split_ is escalated to a stance-assigned debate rather than resolved by chairman fiat. Debate output = an explicit crux for the human, not a forced consensus.
3. **T8 inside anything.** Tool-augmented critique is a modifier, not a competitor: give every reviewer in every technique execution and retrieval tools. It converts a fraction of every review from opinion to fact at near-zero design cost.
4. **T6 as the inner selector of T3's fix pass.** Generate 3 candidate revisions of a flagged section, select with a rubric verifier. Cheap where sections are short.
5. **Anti-composition:** T9 wrapped around any of the above adds stages without adding independence — the one stacking direction the failure literature consistently punishes.

---

## 6. Failure Modes (per technique, with mitigations)

| Technique          | Dominant failure mode                                                                                                                    | Mitigation                                                                                                       |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| T1 Gauntlet        | Anchoring on prior rounds; style churn after round ~3; sanding off justified opinions to appease reviewers                               | Convert to T3; hard cap rounds; pre-register what counts as a defect                                             |
| T2 Council         | Verbosity bias in ranking (long vague > short accurate); no revision; consensus papering over cruxes                                     | Rubric-anchored ranking prompts; use only as an inner round                                                      |
| T3 Hybrid          | Triage prompt becomes the single point of failure; combination unvalidated                                                               | Log per-round novel-issue counts to self-validate; rotate triage model                                           |
| T4 Debate          | Thought degeneration (agents entrench); herding to majority                                                                              | Assigned stances; agreement-ratio modulation; consensus-free scoring                                             |
| T5 MoA             | Averaging away strong correct positions; no error _detection_                                                                            | Follow with a critique-shaped pass; never terminal for factual content                                           |
| T6 Best-of-N       | Verifier gaming / Goodharting; blind where no scorer exists                                                                              | Multiple diverse verifiers; restrict to scorable slices                                                          |
| T7 Self-Refine     | Self-preference; rewriting instead of correcting; degrading correct answers                                                              | External signal only (tests, tools, second model); cap at one pass                                               |
| T8 Tool Critique   | False confidence spillover — tool-verified slices lend unearned credibility to unverified ones                                           | Mark verified vs. opined claims explicitly in review output                                                      |
| T9 Role-Pipeline   | Error propagation; cost explosion; debugging opacity                                                                                     | Per-stage validation gates; collapse stages until it hurts                                                       |
| T10 Mechanical     | Coverage illusion — testing the checkable 30% and calling the artifact verified                                                          | Explicit inventory of what was and was not checked                                                               |
| _All of the above_ | **Shared prior collapse**: all-LLM ensembles converge to training-distribution consensus; cross-family diversity helps less than assumed | Humans with skin in the game; empirical contact; adversarial "takedown" generation targeting framing-level error |

---

## 7. Recommendation

For document and specification refinement — the primary use case:

1. **Default loop: T3 (Parallel-Blind Hybrid).** 3–4 independent blind reviews per round, triage by independent-flag count, revise, stop when a round yields ≤ 1 new high-severity issue.
2. **Always-on modifiers: T8 and T10.** Every checkable claim gets checked, not reviewed. Reviewers get tools. Verified and opined findings are labeled separately.
3. **Escalation path: T4.** Reviewer splits go to stance-assigned debate; the output is a crux presented to the human, never a synthesized compromise.
4. **Upstream: T5.** For new documents, merge 2–3 independent drafts before the first review round.
5. **One structural takedown per artifact.** Before the final round, commission a single adversarial "this document is fundamentally wrong" pass — critique-mode reviewers work within the frame; only takedown-mode reliably attacks it.
6. **Know the ceiling.** All-LLM review converges to model-consensus quality. The two diversity injections that break the ceiling — humans with stakes, and reality — should be scheduled, not hoped for.

Net: no single published technique dominates. The dominant _strategy_ is a composition — mechanical verification wherever possible, parallel-blind review with a stopping rule for everything else, debate for disagreements, and synthesis only at the drafting stage.

---

## 8. Sources

- Karpathy, A. _llm-council_ (GitHub, 2025) — three-stage council: independent answers, anonymized peer ranking, chairman synthesis; author-labeled weekend hack, no benchmarks.
- Du, Y. et al. _Improving Factuality and Reasoning in Language Models through Multiagent Debate_ (2023).
- Wang, J. et al. _Mixture-of-Agents Enhances Large Language Model Capabilities_ (ICLR 2025).
- Smit, A. et al. _Should We Be Going MAD? A Look at Multi-Agent Debate Strategies for LLMs_ (ICML 2024) — agreement modulation.
- Lifshitz, S., McIlraith, S., Du, Y. _Multi-Agent Verification: Scaling Test-Time Compute with Multiple Verifiers_ (COLM 2025).
- Cemri, M. et al. _Why Do Multi-Agent LLM Systems Fail?_ (NeurIPS 2025).
- _Multi-Agent Debate for LLM Judges with Adaptive Stability Detection_ (NeurIPS 2025) — principled adaptive stopping for debate-based judging.
- Chan, C.-M. et al. _ChatEval: Towards Better LLM-Based Evaluators through Multi-Agent Debate_ (ICLR 2024).
- _Free-MAD: Consensus-Free Multi-Agent Debate_ (2025).
- Madaan, A. et al. _Self-Refine_ (2023); Shinn, N. et al. _Reflexion_ (2023) — gains depend on external feedback signals.
- Gou, Z. et al. _CRITIC: Large Language Models Can Self-Correct with Tool-Interactive Critiquing_ (2023).
- Wang, X. et al. _Self-Consistency Improves Chain of Thought Reasoning_ (ICLR 2023).
- Zheng, L. et al. _Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena_ (2023) — verbosity and self-preference biases in model judges.
- Irving, G., Christiano, P., Amodei, D. _AI Safety via Debate_ (2018) — the original debate proposal.
- Deliberative multi-agent LLMs improve clinical reasoning in ophthalmology (2026) — independent validation of the Council pipeline in one domain.

_Scores in §3 are this document's synthesis, not values taken from any single paper. Weighted totals are exact under the stated weights; re-derive them if you change a weight._
