# Local Coding Model Decision Matrix — v2

**Target hardware:** Ryzen 7 5800X3D · RTX 3080 Ti 12 GB VRAM · 64 GB DDR4-3200 (dual channel, ~50 GB/s)
**Goal:** maximize coding-agent quality per wall-clock hour, minimize hallucinated output, reduce dependence on paid frontier services.

---

## The constraint that drives everything

Decode speed on a hybrid GPU/CPU setup is bound by **how many bytes per token must stream from system RAM**, not by CPU core count. DDR4-3200 dual channel delivers ~50 GB/s. This produces an inversion of the usual "dense beats MoE locally" intuition:

- **Dense 24B (Devstral) at Q4_K_M is ~15 GB.** It cannot fit in 12 GB alongside KV cache, so ~4–6 GB of layers spill to RAM — and a dense model touches *every* weight per token. The spilled portion caps decode at roughly **6–10 tok/s**, no matter what else you tune.
- **A3B MoE models (GLM-4.7-Flash, Qwen3-Coder-Next) only touch ~3B active parameters per token.** With routed experts placed in RAM and attention + shared experts + KV cache kept on GPU, only ~1.5–2 GB streams from RAM per token → **~15–25 tok/s** on the same memory bus. The 12 GB card holds the hot path comfortably.

On a 24 GB card this matrix would look different. On *this* card, MoE-with-expert-offload is the architecture that fits, and the dense model is the one paying the CPU-offload tax.

A second consequence: for agentic loops (read → plan → edit → compile/test → fix), throughput compounds. An agent iterating 30–60 times per task at 8 tok/s is qualitatively worse to use than the same loop at 20 tok/s, and often loses on wall-clock-to-working-patch even against a smarter-per-turn model.

---

## Decision matrix

| Model | SWE-bench Verified | Footprint (Q4) | Est. decode on this box | Role | Decision |
|---|---|---|---|---|---|
| **GLM-4.7-Flash** (30B-A3B MoE) | 59.2% | ~17 GB total; hot path fits in 12 GB VRAM with expert offload | **~15–25 tok/s** | Daily-driver coding agent | **PRIMARY** |
| **Devstral Small 2 24B** (dense) | 68.0% | ~15 GB; forced 4–6 GB spill to RAM | ~6–10 tok/s | Local quality escalation for hard single tasks | **KEEP (secondary)** |
| **Qwen3-Coder-Next 80B-A3B** (MoE) | ~70.7% (Qwen-reported) | ~44–48 GB; experts in RAM, attention on GPU | ~8–15 tok/s today (runtime-limited) | Quality leader; scheduled re-test as llama.cpp matures | **RE-TEST QUARTERLY** |
| Qwen3-Coder-30B-A3B | ~50–51.6% (scaffold-dependent) | ~17 GB | ~15–25 tok/s | Dominated by GLM-4.7-Flash at identical footprint, plus known tool-call formatting issues in some scaffolds | **DROP** |
| Devstral 2 123B (dense) | higher, irrelevant | 60–75+ GB dense — every token crawls through RAM | ~1–3 tok/s | None | **SKIP** |

### Why GLM-4.7-Flash is primary

It is the only model on the list where the quality tier and the hardware tier line up. 59.2% SWE-bench Verified with ~3.6B active parameters, ~200K context ceiling, strong tool-calling (τ²-Bench 79.5), and a hot path that fits *entirely* in 12 GB — meaning prompt processing stays fast and decode speed holds up at agent-realistic context depths. The 9-point quality gap to Devstral is real, but it buys a 2–3× throughput advantage that the agent loop converts into more iterations, more test runs, and more self-correction per hour — which is where hallucination actually gets caught.

### Why Devstral stays but doesn't win

68.0% SWE-bench Verified is the best score in the ≤30B class and its repo-exploration/multi-file-editing tuning is exactly the target workload. Keep it loaded for escalation: when GLM-4.7-Flash fails a task twice, retry once with Devstral before escalating to a paid frontier model. Just don't make a 7 tok/s model the thing you sit in front of all day.

### Why the 80B is a scheduled re-test, not an experiment to forget

It's the strongest model that physically fits this machine — ~70.7% SWE-bench Verified, above Devstral — and 64 GB RAM is exactly the capacity it needs at Q4. What holds it back today is **runtime maturity, not your hardware**: llama.cpp's CPU path for the qwen3next hybrid-attention architecture currently underperforms bandwidth math by ~3–5× (tracked in llama.cpp issue #19480), and the ik_llama.cpp fork already reports ~1.9× faster MoE inference. When that gap closes, this becomes the primary. Re-test with each major llama.cpp release; try ik_llama.cpp now.

---

## Runtime

**llama.cpp (CUDA build), latest release.** The one flag family that matters on this hardware and that v1 of this document missed entirely: **expert offload** (`--n-cpu-moe N` or `-ot "exps=CPU"`). The strategy for MoE models is *not* "maximize layer offload" — it's "everything on GPU except routed experts."

Tuning procedure: start with all expert layers on CPU (`--n-cpu-moe 99`), confirm it runs, then decrease N stepwise to pull expert layers back onto the GPU until `nvidia-smi` shows ~11 GB used. Each layer pulled back is free speed.

### Primary config — GLM-4.7-Flash

```bash
llama-server \
  -m GLM-4.7-Flash-UD-Q4_K_XL.gguf \
  -ngl 99 --n-cpu-moe 99 \        # then lower N until VRAM ~11 GB
  -fa on \
  -c 65536 \
  --cache-type-k q8_0 --cache-type-v q8_0 \
  --temp 1.0 --top-p 0.95 --min-p 0.01 \   # Z.ai-recommended sampling
  --jinja \
  --alias glm-4.7-flash --port 8080
```

Use the Unsloth UD-Q4_K_XL GGUF (their fixed/re-uploaded version — early GGUFs had a chat-template bug). Requires a recent llama.cpp build (the GLM-4.7 flash-attention CUDA fix landed late Jan 2026).

### Escalation config — Devstral Small 2

```bash
llama-server \
  -m Devstral-Small-2-24B-Instruct-2512-Q4_K_M.gguf \
  -ngl 28 \                        # tune: raise until VRAM ~11 GB, then back off 1
  -fa on \
  -c 32768 \
  --cache-type-k q8_0 --cache-type-v q8_0 \
  --temp 0.15 \
  --jinja \
  --alias devstral-small-2 --port 8081
```

Note the KV/weight tension unique to the dense model: every extra 16K of context is VRAM that evicts weight layers to RAM and slows decode further. 32K is the sweet spot; go higher only for tasks that demand it.

### Re-test config — Qwen3-Coder-Next 80B

```bash
llama-server \
  -m Qwen3-Coder-Next-Q4_K_M.gguf \
  -ngl 99 --n-cpu-moe 99 \
  -fa on \
  -c 32768 \
  --cache-type-k q8_0 --cache-type-v q8_0 \
  --temp 0.7 --top-p 0.8 --top-k 20 --repeat-penalty 1.05 \
  --jinja --port 8082
```

Benchmark the same setup on ik_llama.cpp and keep whichever is faster. Expect this one to improve release-over-release.

---

## Context policy

16K is too small to even start for agentic work — repo exploration plus tool output routinely burns 30–60K. Baseline at **32K, benchmark 64K**, with q8_0 KV cache quantization throughout (negligible quality cost, halves KV memory). GLM-4.7-Flash supports ~200K and Devstral 256K, but on this hardware the practical ceiling is where prompt-processing latency at full context starts dominating the loop — measure it, don't assume it.

---

## Agent layer

OpenCode (or Mistral Vibe when running Devstral) against the local llama-server OpenAI-compatible endpoint. Non-negotiable loop shape: **read → plan → edit → compile/test → inspect failures → fix → retest**, with test/compile verification mandatory before any patch is accepted.

One framing correction from v1: SWE-bench measures task completion, not hallucination rate. The anti-hallucination mechanism in this stack is the verification loop itself — a model that can run the tests and see them fail catches its own confabulation. This is another reason throughput matters: more iterations = more verification passes per task.

## Evaluation protocol

Don't crown a winner from benchmark tables — the two candidates are close enough that scaffold and repo characteristics decide it. Run both primaries on 5–10 real tasks from your actual repos and measure:

1. **Wall-clock to verified working patch** (the only metric that matters)
2. Decode tok/s and prompt-processing tok/s at 32K context
3. Task success rate without human intervention
4. Tool-call format error rate

Prediction to falsify: GLM-4.7-Flash wins metric 1 on most tasks despite losing the per-turn quality comparison.

## Escalation ladder

1. GLM-4.7-Flash (default, fast, free)
2. Devstral Small 2 (second attempt on failures)
3. Paid frontier model (hard architecture/debugging tasks only)

## Hardware upgrade path

**VRAM is the only upgrade that changes the matrix.** A used RTX 3090 (24 GB) puts GLM-4.7-Flash fully on-GPU (~17 GB at 4K ctx, ~23 GB at 65K), fits Devstral entirely with room for KV, and turns both into 40–80 tok/s models. That upgrade is worth more than 64→128 GB RAM, faster RAM, or a CPU swap — decode is bandwidth-bound, and VRAM bandwidth (~912 GB/s on the 3080 Ti) is ~18× the DDR4 bus. The 80B is the exception: it benefits from RAM *bandwidth*, which on AM4 is effectively capped — another reason it waits on runtime optimization rather than hardware.

---

## Key references

- GLM-4.7-Flash local guide (Unsloth): https://unsloth.ai/docs/models/tutorials/glm-4.7-flash
- GLM-4.7-Flash hardware behavior (VRAM/context scaling): https://www.hardware-corner.net/glm-4-7-flash-llm-hardware/
- GLM-4.7-Flash + OpenCode setup: https://www.pondhouse-data.com/blog/glm-4-7-flash-local-opencode
- Devstral Small 2 GGUF: https://huggingface.co/unsloth/Devstral-Small-2-24B-Instruct-2512-GGUF
- Devstral 2 run guide (Unsloth): https://unsloth.ai/docs/models/tutorials/devstral-2
- Qwen3-Coder-Next GGUF: https://huggingface.co/Qwen/Qwen3-Coder-Next-GGUF
- Qwen3-Coder-Next llama.cpp CPU perf issue: https://github.com/ggml-org/llama.cpp/issues/19480
- ik_llama.cpp (faster MoE CPU inference): https://github.com/ikawrakow/ik_llama.cpp
- llama.cpp: https://github.com/ggml-org/llama.cpp
