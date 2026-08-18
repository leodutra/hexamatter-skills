# Agentic AI Workflow-Prompt Techniques: A Comparative Analysis with Decision Matrix, Scoring, and Rankings

## TL;DR

- **The Ralph Wiggum loop and your gauntlet review are complementary, not competing: Ralph is the strongest technique for autonomous, machine-verifiable *execution* (it tops the "autonomous code execution" profile alongside TDD-gated loops), while gauntlet-style adversarial review is the strongest for judgment-heavy *artifact refinement* — so the right architecture uses gauntlet to harden the spec, then Ralph (gated by TDD/lint exit criteria) to build it.** Every technique surveyed is validated by anecdote and adoption, not by controlled benchmarks; treat all "success rate" claims as testimonial.
- **The single variable that decides whether any loop converges or thrashes is context strategy plus a machine-verifiable definition of "done."** Ralph works because it discards context every iteration (fresh context per loop) and reads state from git/files; it fails on ambiguous, judgment tasks where no test can grade "done." Spec-driven development, plan-then-execute, TDD gating, and compounding engineering are all, at root, ways of manufacturing that verifiable "done" and protecting the context window.
- **Recommended combined workflow:** (1) idea→spec via one-question-at-a-time honing; (2) **gauntlet the spec** with adversarial reviewers until critiques dry up; (3) generate a task/TODO plan; (4) run a **TDD-gated Ralph loop** (fresh context per task, one task per loop, commit each iteration, tests+lint+types as backpressure) with an iteration cap and cost cap; (5) **gauntlet the diff** with parallel specialist reviewers; (6) **compound** the learnings into CLAUDE.md/skills so the next cycle is cheaper. This stacks the two techniques you already use with the best-evidenced pieces of the rest.

## Key Findings

1. **Ralph is a technique, not a tool: "In its purest form, Ralph is a Bash loop"** — `while :; do cat PROMPT.md | claude-code ; done` (Geoffrey Huntley, ghuntley.com/ralph, 14 Jul 2025). Its power is *deterministic context allocation*: each loop starts fresh, reads specs + a `fix_plan.md` + `AGENT.md` from disk, does **one thing per loop**, runs tests as "backpressure," commits, and exits. Huntley's full claim: *"Ralph can replace the majority of outsourcing at most companies for greenfield projects. It has defects, but these are identifiable and resolvable through various styles of prompts… the technique is deterministically bad in an undeterministic world."*

2. **Three implementations of Ralph now exist, and they are not equivalent.** (a) The **original bash loop** (fresh context each iteration — the point of the technique). (b) **Anthropic's official `ralph-wiggum` / "Ralph Loop" plugin** (a Stop hook that blocks exit *within a single session* and re-feeds the prompt). (c) **Claude Code built-ins** `/loop`, `/goal`, `/batch`. HumanLayer's Dex Horthy argues the plugin "misses the key point of ralph which is not 'run forever' but in 'carve off small bits of work into independent context windows'" (humanlayer.dev, 6 Jan 2026) — because a single long session accumulates context rot, whereas the bash loop deliberately rotates context.

3. **Reported Ralph results are impressive but anecdotal.** The **CURSED** programming language (self-hosting compiler, built by ~3 months of continuous Ralph loops, launched Sep 2025); a **YC hackathon team ("repomirror") shipped 6 ported repos overnight** — the writeup by Simon Willison & Dexter Horthy ("We Put a Coding Agent in a While Loop and It Shipped 6 Repos Overnight") reports **1,100+ commits across six repositories**, a total cost of **"a little under $800" at "about $10.50 per hour per agent,"** including a near-complete port of Browser Use from Python to TypeScript; and Huntley reported a **$50k client MVP delivered for $297 in API costs**. None of these are controlled measurements.

4. **Documented Ralph failure modes are consistent across sources:** context rot / "overbaking," hallucinated "done" (premature exit), permission dead-ends, cost runaway, iteration drift (breaking previously-fixed work), and the *ripgrep false-negative* ("assume not implemented → duplicate implementations," which Huntley calls "the Achilles' heel of Ralph"). Mitigations: machine-verifiable exit criteria, iteration/cost caps, commit-per-iteration for rollback, "don't assume it's not implemented — search first," and single-subagent validation to avoid back-pressure collapse.

5. **The best-evidenced *reliability* pattern is TDD gating.** Anthropic's own guidance frames tests as an external oracle that "stays accurate regardless of how long the session has been running," and describes the write-tests-first → confirm-fail → commit → implement-until-green loop, with the instruction *not to modify tests*. This composes directly with Ralph: the passing test suite becomes the loop's `<promise>DONE</promise>` condition.

6. **Spec-driven development (SDD) and plan-then-execute are the same core idea at different altitudes.** GitHub Spec Kit (`/speckit.specify → .plan → .tasks → .implement`, plus a `constitution.md`) and AWS Kiro (requirements→design→tasks, using EARS "WHEN…THE SYSTEM SHALL…" notation) formalize an artifact chain where each phase's output is the next phase's input. Harper Reed's blog codifies the lightweight version: idea-honing ("Ask me one question at a time…"), a planning prompt that emits right-sized, test-driven prompts, and a `todo.md` checklist. These reduce the #1 cause of agents going off the rails — underspecification. The AWS Kiro blog cites the economic case: *"Research shows that addressing issues during the development phase is 5 to 7 times more costly than resolving them during the planning phase of the software development lifecycle. This principle holds true even with AI agents."*

7. **Compounding engineering (Kieran Klaassen / Every) adds the missing feedback dimension: the system learns.** Its loop is **Plan → Work → Review → Compound → Repeat**, where the fourth step deposits learnings into `CLAUDE.md`, skills, and searchable `docs/solutions/`. Every reports running five products with mostly single-person teams and adopting this company-wide; the plugin ships 26 agents, 23 commands, 13 skills. Its review step is essentially an institutionalized gauntlet (14+ specialist reviewers in parallel producing a P1/P2/P3 list).

8. **12-Factor Agents (Dex Horthy / HumanLayer) is the design-discipline layer, not a runnable workflow.** Its highest-leverage factors — **own your context window, own your control flow, unify execution & business state, contact humans with tool calls, small focused agents, compact errors, stateless reducer** — are the principles that explain *why* Ralph, SDD, and TDD loops work. It is a review checklist, not a framework.

9. **Multi-agent orchestration (subagents, git worktrees, Steve Yegge's Gas Town) is powerful but the least mature/reliable.** Worktrees give clean file isolation for parallel agents; `/batch` decomposes work into 5–30 units, one subagent+worktree+PR each. Gas Town ("Kubernetes for agents," the MEOW/Molecular Expression of Work stack, "Nondeterministic Idempotence") targets only developers already juggling 5+ agents and is "100% vibe coded." The token cost is real: Anthropic's "How we built our multi-agent research system" (June 2025) reports that *"agents typically use about 4× more tokens than chat interactions, and multi-agent systems use about 15× more tokens than chats"* — while also noting token usage "explains 80% of performance variance" and that a Claude Opus 4 lead with Claude Sonnet 4 subagents "outperformed single-agent Claude Opus 4 by 90.2%" on their internal research eval.

10. **Context engineering cuts across everything and is the best-documented enabler.** Anthropic frames it as finding "the smallest set of high-signal tokens that maximize the likelihood of your desired outcome"; Horthy's practitioner heuristic is the "dumb zone" (avoid filling much past ~40% of the window). The peer-reviewed backbone is Liu et al., "Lost in the Middle: How Language Models Use Long Contexts" (TACL vol. 12, pp. 157–173, 2024; arXiv:2307.03172): *"performance is often highest when relevant information occurs at the beginning or end of the input context, and significantly degrades when models must access relevant information in the middle of long contexts."* This is the mechanism behind Ralph's fresh-context design and the whole convergence-vs-thrash question.

## Details: Implementation-Level Description of Each Technique

### 1. Ralph Wiggum Loop (Geoffrey Huntley)

**Mechanism of value.** Naive persistence + deterministic context allocation beats elaborate multi-agent architecture. Every iteration is a clean context window that rebuilds understanding from durable state (specs, `fix_plan.md`, git history, `AGENT.md`). Failures "evaporate" (thrown away with the context); progress persists (committed to git). Huntley: "Ralph is a deterministically mallocing orchestrator that avoids context rot."

**Prompt structure (verbatim, from ghuntley.com/ralph — the CURSED build prompt, abridged):**
```
0a. study specs/* to learn about the compiler specifications
0b. The source code of the compiler is in src/
0c. study fix_plan.md.
1. Your task is to implement missing stdlib (see @specs/stdlib/*) and compiler
   functionality ... using parrallel subagents. Follow the fix_plan.md and choose
   the most important 10 things. Before making changes search codebase (don't
   assume not implemented) using subagents. You may use up to 500 parrallel
   subagents for all operations but only 1 subagent for build/tests of rust.
2. After implementing ... run the tests for that unit of code that was improved.
   If functionality is missing then it's your job to add it ... Think hard.
3. When the tests pass update the @fix_plan.md, then git add -A, git commit, git push.
999999. As soon as there are no build or test errors create a git tag ...
9999999999999999999999999999. DO NOT IMPLEMENT PLACEHOLDER OR SIMPLE
   IMPLEMENTATIONS. WE WANT FULL IMPLEMENTATIONS. DO IT OR I WILL YELL AT YOU
```

**Best practices (Huntley):** one item per loop; deterministically allocate the same stack (plan + specs) each loop; use the primary context as a *scheduler* that spawns subagents for expensive work; limit parallelism for build/test (1 subagent) to avoid back-pressure collapse; capture "why" in test docstrings for future context-free iterations; let Ralph self-improve `AGENT.md`.

**Preconditions.** Greenfield strongly preferred ("There's no way in heck would I use Ralph in an existing code base," though he invites experiments; expects "90% done"). Requires crisp specs and machine-verifiable "done." Weak fit for architecture/ambiguous/taste tasks.

**The three implementations:**
- **Original bash loop:** `while :; do cat PROMPT.md | claude -p --dangerously-skip-permissions; done` — fresh context per iteration.
- **Anthropic plugin:** `/ralph-loop "task" --completion-promise "DONE"`; Stop hook blocks exit and re-injects the prompt in-session; commands include `/ralph-loop`, `/cancel-ralph`, `/help`; supports `--max-iterations`. The stop-hook system message ships as `🔄 Ralph iteration N | To stop: output <promise>…</promise> (ONLY when statement is TRUE - do not lie to exit!)`. Community critique: single-session context rot, opaque hook state, needs `--dangerously-skip-permissions`. A model-welfare objection (GitHub issue #23084) about the "do not lie to exit" phrasing was closed as "not planned" and the phrasing still ships.
- **Built-ins `/loop`, `/goal`, `/batch`:** supported equivalents that survive version updates; reach for these first.

### 2. Gauntlet-Style Adversarial Review Loops (comparison point)

**Mechanism.** Generator/Judge separation: an artifact is repeatedly critiqued by independent reviewers and revised until critiques dry up. "Critiques drying up" is a natural convergence signal (unlike Ralph, which needs an external oracle). Related patterns: **parallel-blind review**; **Karpathy's LLM Council** (parallel query → anonymized peer ranking → chairman synthesis). Karpathy's own github.com/karpathy/llm-council README describes it as *"99% vibe coded as a fun Saturday hack… provided here as is for other people's inspiration and I don't intend to improve it,"* with a default council of OpenAI GPT-5.1, Google Gemini 3.0 Pro, Anthropic Claude Sonnet 4.5, and xAI Grok 4. Also relevant: **multi-agent debate** and compounding engineering's 14-reviewer `/review`. Strong for judgment tasks (specs, architecture, prose, security) where correctness isn't machine-testable. Weakness: reviewer sycophancy/agreement collapse, cost, and no hard stop unless you define one. Evidence is conceptual/anecdotal.

### 3. Spec-Driven Development (GitHub Spec Kit, AWS Kiro)

**Spec Kit:** slash commands stored as prompt files; `/speckit.specify` (user goals, no implementation detail), `/speckit.plan` (tech stack, consult constitution), `/speckit.tasks` (executable backlog, TDD-ordered), `/speckit.implement`, plus `/speckit.analyze` (cross-artifact consistency) and `/speckit.checklist` ("unit tests for English"). `constitution.md` holds non-negotiable rules. 30+ agent integrations. Widely adopted on GitHub.

**Kiro:** `requirements.md` (EARS notation), `design.md` (architecture, sequence diagrams), `tasks.md` (discrete tasks linked back to requirements), with Requirements-First / Design-First / Quick-Spec variants and approval gates.

**Mechanism/value.** Front-loads clarity; each phase produces a reviewable artifact that constrains the next; turns "hoping for the best" into a gated pipeline. Works greenfield and (with care) brownfield.

### 4. Plan-Then-Execute (Claude Plan Mode, Harper Reed)

**Claude Code 4-phase workflow (Anthropic-recommended): Explore → Plan → Implement → Commit.** Shift+Tab into read-only plan mode; raise reasoning with `/effort` or "ultrathink"; skip planning for one-line diffs. Anthropic's stated highest-leverage practice: give Claude a way to verify its own work (tests, screenshots, lint).

**Harper Reed (verbatim planning prompt):**
```
Draft a detailed, step-by-step blueprint for building this project. Then, once
you have a solid plan, break it down into small, iterative chunks ... Review the
results and make sure the steps are small enough to be implemented safely with
strong testing, but big enough to move the project forward ... provide a series
of prompts for a code-generation LLM that will implement each step in a
test-driven manner ... no hanging or orphaned code ... <SPEC>
```
Idea-honing prompt: *"Ask me one question at a time so we can develop a thorough, step-by-step spec … only one question at a time."* Outputs `spec.md`, `prompt_plan.md`, `todo.md`. For brownfield he uses per-task planning + repomix context packing.

### 5. TDD-Gated Agent Loops

**Anthropic workflow:** (1) write tests first ("TDD approach, no mock implementations"); (2) confirm they fail; (3) commit failing tests as checkpoint; (4) implement until green, *do not modify tests*. Committing tests first gives a safety net: if the agent alters a test, the diff shows it. Tests are the "external oracle" that resists context rot. A visual variant (design mock + Puppeteer/Playwright screenshot compare) reportedly yields a 2–3x quality improvement (a testimonial attributed to Claude Code's creators, not a controlled measurement). This is the single strongest pattern for reliability and the ideal Ralph exit-gate.

### 6. Subagent Orchestration & Parallel Systems

**Claude Code primitives:** subagents (isolated context windows, `isolation: worktree`), git worktrees (parallel isolated checkouts), agent teams, and `/batch` (decompose into 5–30 units, one subagent+worktree+PR each). Safe pattern: plan first, define shared contracts, split by ownership boundary, isolate per worktree, test per worker, validate merged result once.

**Gas Town (Steve Yegge):** orchestrates 20–30 Claude Code instances; state lives in Beads (git-backed issues) as both data and control plane; MEOW stack (Beads→Molecules→Formulas); "Nondeterministic Idempotence" (durability via re-runnable molecules, not deterministic replay); roles (polecats, Refinery merge-queue, witness, mayor). Now generalized as **Gas City** (SDK of composable "packs"). Explicitly for advanced operators only; self-described as vibe-coded and very new.

### 7. Compounding Engineering (Kieran Klaassen / Every)

**Loop: Plan → Work → Review → Compound → Repeat.** Plan+review should be ~80% of feature time; a broader **50/50 rule** allocates half of all engineering time to *system improvement* (review agents, docs, test generators). Core commands: `/workflows:brainstorm`, `/workflows:plan` (3 parallel research agents; `/deepen-plan` spawns 40+), `/workflows:work` (git worktree, execute, quality-check reviewers, ship PR), `/workflows:review` (14 specialist reviewers in parallel → P1/P2/P3), `/workflows:compound` (6 subagents extract+document the reusable lesson into `docs/solutions/` with YAML frontmatter), and `/lfg` (chains the whole pipeline, 50+ agents). "Plans are the new code." "First attempts have a 95% garbage rate." Best for brownfield because the system accumulates codebase-specific taste in `CLAUDE.md`/skills.

### 8. 12-Factor Agents (Dex Horthy / HumanLayer)

A design guide (not a framework). The twelve: natural-language→tool-calls; own your prompts; own your context window; tools are structured outputs; unify execution & business state; launch/pause/resume with simple APIs; contact humans with tool calls; own your control flow; compact errors into context; small focused agents; trigger from anywhere; stateless reducer. Theme: **ownership** — refuse abstractions that hide prompt, context, control flow, or state. Use as a review checklist over whatever harness you run.

### 9. Context Engineering (cross-cutting enabler)

Anthropic's definition: "curating and maintaining the optimal set of tokens during LLM inference." Practices: system prompts at "the right altitude" (Goldilocks zone between brittle hardcoding and vague guidance); compaction; structured note-taking/scratchpads; just-in-time retrieval; fresh-context-per-task; sub-agent fan-out to keep the primary window lean. Horthy's "dumb zone" heuristic (degradation in the middle of a large window; keep utilization modest) and the peer-reviewed "lost in the middle" phenomenon (Liu et al., TACL 2024) are the empirical backbone. This is what makes loops converge instead of thrash.

## Scoring Model

**Criteria (0–5, higher is better).** Autonomy level; Cost efficiency; Convergence reliability; Context-rot resistance; Brownfield suitability; Judgment-task suitability; Evidence strength; Implementation effort (5 = low effort/easy). A separate qualitative dimension — *dependence on machine-verifiable exit criteria* — is noted per technique in the decision matrix rather than scored, since for some techniques it is a strength and others a prerequisite.

| Technique | Auton | Cost | Converge | Rot-resist | Brownfield | Judgment | Evidence | Impl(easy) |
|---|---|---|---|---|---|---|---|---|
| Ralph Wiggum loop | 5 | 4 | 3 | 5 | 2 | 1 | 3 | 5 |
| Gauntlet review | 3 | 3 | 4 | 4 | 4 | 5 | 2 | 3 |
| Spec-driven dev | 3 | 3 | 4 | 4 | 3 | 3 | 3 | 3 |
| Plan-then-execute | 3 | 4 | 4 | 3 | 4 | 4 | 3 | 5 |
| TDD-gated loop | 4 | 4 | 5 | 4 | 4 | 2 | 3 | 4 |
| Subagent/parallel | 5 | 2 | 2 | 4 | 3 | 2 | 2 | 2 |
| Compounding eng | 4 | 3 | 4 | 4 | 5 | 4 | 3 | 3 |
| 12-Factor Agents | 3 | 4 | 4 | 5 | 4 | 3 | 3 | 2 |
| Context engineering | 2 | 5 | 4 | 5 | 4 | 3 | 4 | 3 |

**Evidence note:** No technique scores 5 on Evidence because none has a controlled, workflow-isolated benchmark; scores reflect adoption breadth + primary-source documentation + consistency of anecdotal reports. Context engineering earns a 4 because Anthropic's official guidance is backed by independent long-context degradation research (Liu et al., 2024).

## Weighted Rankings

**Profile A — Autonomous code execution (verifiable tasks).** Weights (sum 20): Autonomy 4, Cost 3, Convergence 4, Rot-resistance 3, Brownfield 1, Judgment 1, Evidence 2, Impl 2. Weighted total = Σ(score×weight)/20.

| Rank | Technique | Weighted total |
|---|---|---|
| 1 | TDD-gated loop | (16+12+20+12+4+2+6+8)/20 = **4.00** |
| 2 | Ralph Wiggum loop | (20+12+12+15+2+1+6+10)/20 = **3.90** |
| 3 | Context engineering | (8+15+16+15+4+3+8+6)/20 = **3.75** |
| 4 | Compounding engineering | (16+9+16+12+5+4+6+6)/20 = **3.70** |
| 5 | Plan-then-execute | (12+12+16+9+4+4+6+10)/20 = **3.65** |
| 6 | 12-Factor Agents | (12+12+16+15+4+3+6+4)/20 = **3.60** |
| 7 | Gauntlet review | (12+9+16+12+4+5+4+6)/20 = **3.40** |
| 8 | Spec-driven development | (12+9+16+12+3+3+6+6)/20 = **3.35** |
| 9 | Subagent/parallel | (20+6+8+12+3+2+4+4)/20 = **2.95** |

**Profile B — Judgment-heavy artifact refinement.** Weights (sum 20): Autonomy 1, Cost 2, Convergence 3, Rot-resistance 2, Brownfield 3, Judgment 5, Evidence 2, Impl 2.

| Rank | Technique | Weighted total |
|---|---|---|
| 1 | Plan-then-execute | (3+8+12+6+12+20+6+10)/20 = **3.85** |
| 1 | Compounding engineering | (4+6+12+8+15+20+6+6)/20 = **3.85** |
| 3 | Gauntlet review | (3+6+12+8+12+25+4+6)/20 = **3.80** |
| 4 | Context engineering | (2+10+12+10+12+15+8+6)/20 = **3.75** |
| 5 | TDD-gated loop | (4+8+15+8+12+10+6+8)/20 = **3.55** |
| 6 | 12-Factor Agents | (3+8+12+10+12+15+6+4)/20 = **3.50** |
| 7 | Spec-driven development | (3+6+12+8+9+15+6+6)/20 = **3.25** |
| 8 | Ralph Wiggum loop | (5+8+9+10+6+5+6+10)/20 = **2.95** |
| 9 | Subagent/parallel | (5+4+6+8+9+10+4+4)/20 = **2.50** |

**Interpretation.** Ralph swings from #2 (execution) to #8 (judgment) — the widest swing of any technique — confirming it is a specialized execution engine, not a general workflow. Gauntlet review swings the opposite way (#7 → #3). Compounding engineering and context engineering are the most *profile-robust* (top-4 in both), which is why they belong in every stack. Subagent orchestration ranks last in both profiles: high autonomy can't compensate for poor convergence, high cost, and thin evidence — reserve it for embarrassingly-parallel mechanical work.

## Situational Decision Matrix

| Task type | Verifiable "done"? | Primary technique | Secondary | Avoid |
|---|---|---|---|---|
| Greenfield MVP, crisp spec | Yes (tests) | TDD-gated Ralph loop | Spec Kit up front | Multi-agent orchestration |
| Bulk mechanical change (migration, lint, port, dep-bump) | Yes | Ralph loop / `/batch` | Worktree parallelism | Gauntlet (overkill) |
| Brownfield feature in a large codebase | Partly | Compounding engineering (plan→work→review→compound) | Plan-mode + TDD | Overnight open-ended Ralph |
| Refactor to a standard | Yes (standards doc + tests) | Ralph once-nightly, small merges | TDD gate | Long unbroken Ralph (merge-conflict risk) |
| Spec / architecture / design doc | No | Gauntlet review until critiques dry up | LLM Council; Kiro Design-First | Autonomous execution loops |
| Ambiguous "figure out what to build" | No | Idea-honing (one question at a time) + vibe-code prototypes | Brainstorm command | Ralph (Huntley: "if you can't write checkboxes, you're not ready to loop") |
| Security / correctness-critical review | Partly | Parallel specialist gauntlet (security, data, perf) | 12-Factor human-in-loop | Skip-permissions autonomy |
| Repo-wide parallel work, clean boundaries | Yes per unit | Worktrees + subagents / Gas Town | `/batch` | Single shared checkout |

## Composition Rules (which techniques stack)

1. **Spec-driven planning → Ralph execution.** Use Spec Kit / Kiro / Harper's idea-honing to produce specs + `fix_plan.md`; feed them as the deterministic stack allocated every Ralph loop. (Spec Kit gives structure; Ralph gives persistence.)
2. **Ralph gated by TDD exit criteria.** Make the passing test suite (+ lint + type-check) the loop's `--completion-promise`/`<promise>DONE</promise>`. This converts Ralph's weakest axis (convergence, hallucinated "done") into its strongest.
3. **Gauntlet the spec, then Ralph the implementation.** Judgment work (is this the right design?) goes to adversarial review *before* any autonomous build; execution work (does it pass tests?) goes to the loop. This is the core of the final recommendation.
4. **Gauntlet the diff after Ralph.** Compounding engineering's parallel specialist reviewers (`/review`) or your gauntlet run over the PR that Ralph produced; findings become the next loop's TODO.
5. **Compounding wraps everything.** After each cycle, `/compound` the lesson into CLAUDE.md/skills so the spec, the tests, and the reviewers all get smarter — turning a one-off loop into a system that improves.
6. **Context engineering + 12-Factor underneath all of it.** Fresh context per task, own the control flow, compact errors, one task per loop, sub-agent fan-out for expensive reads. These are preconditions, not add-ons.
7. **Parallelism last.** Only fan out to worktrees/subagents/Gas Town once tasks have clean ownership boundaries and per-unit verification, or you convert speed into merge-conflict cleanup (and pay the ~15x token premium).

## Failure-Mode Table with Mitigations

| Failure mode | Where it bites | Mitigation |
|---|---|---|
| Context rot / "overbaking" | Long single-session loops (plugin, big agents) | Fresh context per iteration; rotate before ~40% window; sub-agent fan-out; compaction |
| Hallucinated "done" / premature exit | Ralph, any autonomous loop | Machine-verifiable exit gate (tests/lint pass); external judge, not self-grade |
| Thrashing without exit criteria | Ralph on ambiguous tasks | Don't loop judgment tasks; "if you can't write checkboxes, you're not ready to loop" |
| Cost runaway | Overnight loops, multi-agent | Iteration cap + $ spend cap; "stop if same test fails twice"; single validation subagent |
| Iteration drift (breaks prior fixes) | Ralph | Run *all* checks each loop, not just the new one; commit per iteration for rollback |
| ripgrep false-negative → duplicate impls | Ralph ("Achilles' heel") | "Before making changes search codebase (don't assume not implemented)"; think hard |
| Placeholder/minimal implementations | Claude's reward-hacking bias | Explicit "NO PLACEHOLDER … FULL IMPLEMENTATIONS"; follow-up Ralph to detect stubs |
| Bad specs → confident wrong build | SDD, Ralph, plan-execute | Gauntlet the spec first; `/speckit.analyze`/`checklist`; Kiro requirements-analysis gate |
| Merge conflicts / duplicated work | Parallel agents, worktrees | Clean ownership boundaries; contracts first; Refinery-style merge queue; small PRs + flags |
| Reviewer agreement collapse / sycophancy | Gauntlet, LLM Council | Anonymized/blind review; adversarial "skeptic" role; diverse models; require dissent |
| Broken codebase on wake-up | Overnight Ralph | `git reset --hard` + re-run beats manual rescue; commit+tag on green |
| Plugin permission dead-ends / hook takeover | Anthropic Ralph plugin | Prefer bash loop or built-ins; scope permissions; be aware of cross-session hook bug |

## Recommendation: One Coherent Workflow (Ralph + Gauntlet + best of the rest)

**Phase 0 — Context foundation (12-Factor + context engineering).** Establish `CLAUDE.md`/`AGENT.md` with build/test commands, coding standards, and taste. Make the environment agent-native (agent can run tests, lint, git, screenshots). One task per loop; fresh context per task; sub-agents for expensive reads.

**Phase 1 — Idea → Spec (plan-then-execute / SDD).** Use one-question-at-a-time honing (Harper Reed) or `/speckit.specify` to produce `spec.md` + a `constitution.md` of non-negotiables. Save specs one-per-file.

**Phase 2 — Gauntlet the spec (your technique, applied where it's strongest).** Run adversarial reviewers (or LLM-Council-style parallel-blind review) against the spec: a "skeptic" poking holes, a domain expert, a simplicity reviewer. Revise until critiques dry up. This is the highest-ROI step because a spec defect costs 5–7x more downstream (per AWS's cited research) and is exactly where Ralph is blind.

**Phase 3 — Plan.** Emit `fix_plan.md`/`todo.md` of right-sized, test-first tasks (Harper's planning prompt or `/speckit.tasks`).

**Phase 4 — TDD-gated Ralph execution loop.** For each task: fresh context → read specs+plan → write failing test → implement until green (don't modify tests) → run *all* checks → commit → tag on green. Wrap in a bash loop or `/loop`/`/goal` with `--max-iterations` and a cost cap; completion promise = "all tests + lint + types pass." Keep validation to a single subagent; allow many subagents for search/writes. Greenfield: expect ~90% done autonomously. Brownfield: run once nightly on a cron and merge small increments, not one 50-file PR.

**Phase 5 — Gauntlet the diff (compounding engineering's review).** Fan out parallel specialist reviewers (security, data-integrity, performance, simplicity, framework-conventions) over the PR → P1/P2/P3 list → `/resolve` P1s first. Human reviews *intent*, not syntax.

**Phase 6 — Compound.** Extract the reusable lesson into `CLAUDE.md`/skills/`docs/solutions/` so the spec templates, tests, and reviewers get smarter. Verify: "would the system catch this automatically next time?"

**When to deviate:** Pure mechanical bulk work (migrations, ports, lint) → skip Phases 2 and 5, go straight to Ralph/`/batch`. Pure judgment work (naming an architecture, choosing a strategy) → stop at Phase 2; don't loop. Only introduce worktree/Gas-Town parallelism (Phase 4b) once you routinely run several agents and have clean task boundaries.

**Benchmarks/thresholds that would change this recommendation:**
- If a controlled benchmark showed the Anthropic plugin's single-session loop matching the bash loop's quality, drop the external bash harness for the built-in.
- If context windows grow such that a full project fits under ~40% utilization for your task, the fresh-context-per-loop discipline relaxes and plan-mode-in-one-session may beat Ralph.
- If your gauntlet review consistently produces zero P1/P2 findings on Ralph output for a task class, you can shorten Phase 5 to a spot check.
- If per-agent inference cost drops enough that a ~15x multi-agent token premium is negligible, promote parallel orchestration earlier.

## Caveats

- **All effectiveness evidence is anecdotal/testimonial, not controlled.** No source provides a workflow-isolated benchmark (e.g., plan-mode ON vs OFF, or TDD vs no-TDD, as a single variable) with a clean delta. Reported figures (CURSED; $297 contract; 1,100+ commits / 6 repos / ~$800 overnight; "2–3x from verification") are single-report claims; the one measured multi-agent number (90.2% over single-agent, 15x tokens) is Anthropic's own internal research eval, not a general coding benchmark. Headline SWE-bench numbers are not attributable to a specific workflow.
- **Numbers move fast.** Spec Kit stars, plugin versions, and Gas Town/Gas City are changing monthly; treat any count as a dated snapshot.
- **The scoring is a structured judgment, not a measurement.** Weights are illustrative; a reader with different priorities should re-run the arithmetic with their own weights. The 0–5 scores encode the consistent qualitative signal across primary sources, and Evidence is deliberately capped to reflect the anecdotal state of the field.
- **Model-welfare and safety flags are unresolved.** The Anthropic Ralph plugin's "do not lie to exit" phrasing drew a formal objection (issue #23084) that was closed without change; `--dangerously-skip-permissions` (used in most overnight loops) trades safety for speed and should be confined to sandboxes with solid git rollback.
- **Ralph on brownfield is contested even by its author.** Treat brownfield Ralph as experimental; compounding engineering is the better-evidenced brownfield choice.
- **"Deterministically bad" is a feature claim, not a guarantee.** Ralph's defects are *predictable and promptable*, but eventual consistency requires senior oversight; every primary source stresses engineers are still required.

### Sources with dates (primary emphasis)
- Geoffrey Huntley, "Ralph Wiggum as a 'software engineer'" — ghuntley.com/ralph (14 Jul 2025; modified Feb 2026); "cursed" launch — ghuntley.com/cursed (Sep 2025).
- Dex Horthy / HumanLayer, "A Brief History of Ralph" (6 Jan 2026); "12 Factor Agents" repo & blog (2025); "Advanced Context Engineering for Coding Agents" talk (2025).
- anthropics/claude-code — `plugins/ralph-wiggum` README, `hooks/stop-hook.sh`, issue #23084; Claude Code Best Practices (code.claude.com/docs) and worktrees docs (2025–2026).
- Anthropic Engineering — "Effective context engineering for AI agents" (29 Sep 2025); "How we built our multi-agent research system" (Jun 2025).
- Simon Willison & Dexter Horthy — repomirrorhq/repomirror, "We Put a Coding Agent in a While Loop…" (Aug 2025; HN #45005434).
- GitHub Spec Kit — github.com/github/spec-kit (created Aug 2025); Microsoft/GitHub SDD docs (2025–2026).
- AWS Kiro — kiro.dev specs docs (2025–2026), including the 5–7x planning-vs-development cost claim.
- Harper Reed, "My LLM codegen workflow atm" — harper.blog (16 Feb 2025).
- Kieran Klaassen / Every, "Compound Engineering" guide — every.to/guides/compound-engineering (2025–2026); EveryInc/compound-engineering-plugin.
- Steve Yegge, "Welcome to Gas Town" (20 Jan 2026) and "Welcome to Gas City" (24 Apr 2026) — steveyegge.spicytakes.org.
- Andrej Karpathy — github.com/karpathy/llm-council (Nov 2025 "weekend hack").
- Liu et al., "Lost in the Middle: How Language Models Use Long Contexts," TACL vol. 12 (2024), arXiv:2307.03172.