---
license: mit
language:
- en
- code
library_name: gguf
pipeline_tag: text-generation
tags:
- svelte
- sveltekit
- svelte-5
- runes
- code-generation
- gguf
- mlx
- qwen3
- lora
base_model: Qwen/Qwen3-Coder-14B
base_model_relation: finetune
---

# Svelte Coder

A Svelte 5 / SvelteKit 2 specialist coding model. Free to use under MIT.
Built by [rockypod](https://rockypod.com) on a homelab RTX 3090 Ti using
continuous retrieval-augmented fine-tuning (RAFT) and a correction-stream
methodology — the model is used in real projects, the corrections feed
the next training cycle.

**[HuggingFace — weights](https://huggingface.co/rockypod/svelte-coder)** ·
**[Install via Ollama](https://ollama.com/rockypod/svelte-coder)** — `ollama pull rockypod/svelte-coder` ·
**[GitHub — exam, integration guides, transparency](https://github.com/rockypod/svelte-coder)**

## What's in this repo

- `Modelfile` — Ollama Modelfile with the production ChatML template,
  baked-in system prompt, and inference parameters (temperature 0.2,
  num_predict 1500, repeat_penalty 1.5, num_ctx 8192). Use this on
  Ollama, LM Studio, or any runtime that consumes Modelfiles.
- `integration/` — Setup guides for Continue.dev, LM Studio, Ollama, Zed.
- `LICENSE` — MIT.

The training dataset is **not** included. The exam, exam results, and
README live here for transparency; the synthetic training corpus is
proprietary by design — it is the product of an iterative correction
stream over real-world Svelte work, and that part of the moat stays
private.

## Current scope (v0.9.0)

**In scope.** v0.9.0 covers the working surface of a modern Svelte
project end-to-end:

- Svelte 5 runes — `$state`, `$derived`, `$effect`, `$props`, `$bindable`,
  rune-based utilities (e.g. `useLocalStorage` in class form), the Svelte
  4 → Svelte 5 conversion path
- SvelteKit 2 — file-based routing, `+page.svelte` / `+page.server.ts` /
  `+layout.svelte` / `+server.ts`, `load` (universal and server),
  form actions (the canonical action-triplet pattern), `hooks.server.ts`,
  redirect/error throw rules, `enhance` (action vs function form)
- WCAG 2.2 AAA — semantic landmarks, focus management, ARIA usage,
  color-contrast-aware Tailwind v4 components
- Tailwind v4 — `@theme` tokens, container queries, the v4 `@import` flow
- Hard reasoning — multi-file refactors, regression diagnosis, design
  trade-offs ("what's wrong with this snippet" with structured reasoning)
- Postgres + Redis — typed queries, connection pooling, cache patterns
- D3 dataviz — Svelte-idiomatic D3 (`bind:this` + lifecycle, not D3-managed
  DOM), responsive sizing
- Playwright E2E — page object model, action triplet test patterns,
  `expect.poll` usage
- Svelte Flow — node/edge graph patterns
- Paraglide i18n — message function generation, locale switching in load
- Route file discipline — `+server`, `+page`, `+layout`, hooks
- Snippets and the `{#snippet}` syntax (replaces named slots)

**Deferred to v2.0.** Drupal JSON:API integration patterns and DaisyUI
component library coverage. Both have meaningful surface area and need
their own training cycles to land cleanly — they are explicit roadmap
items, not silent omissions.

## Honest benchmark

Svelte Coder v0.9.0 was evaluated on **two complementary instruments**.
The 30Q spot exam uses a cleaner grader and shorter question set; the
204Q exam is broader and stricter but has known keyword-matching
artifacts that depress scores below real model capability. Both numbers
are disclosed below to give the most honest picture.

| Variant | 30Q spot | 204Q in-scope (rescored) |
|---|---|---|
| **14B** *(recommended)* | **100%** | 70.11% |
| 8B | 82.8% | 74.68% |
| 4B | 79.3% | 67.81% |

The **14B variant is recommended for most users** — it benchmarks
highest on the cleaner instrument by a meaningful margin (~17 points
over 8B). The 8B variant is recommended for hardware where 14B doesn't
fit. The 4B variant trades capability for accessibility on edge
hardware.

**Why two exams?** The 30Q is the cleaner instrument and reflects
production capability; the 204Q is broader and stricter but its
keyword-strict grader produces false negatives — output using slightly
different identifiers (`useLocal()` vs `useLocalStorage()`) or wrapped
in `<think>` reasoning blocks loses points the human eye would award.
We rescore the 204Q once (`<think>` strip + comment strip) and publish
the rescored number; the 30Q needs no such adjustment.

The 204Q ranking shows 8B numerically above 14B (74.68% vs 70.11%) —
that's grader noise, not a real capability difference. The 30Q is the
load-bearing benchmark and the 14B is the strongest variant on it.

**T6 hard reasoning** lands at 41% on the strict 204Q grader for the
14B. Hard reasoning is genuinely the weakest tier — multi-step
refactors with subtle regressions still trip the model. This is honest
disclosure, not a rounding excuse.

## Versioning roadmap

| Version | Bar | What it takes to get there |
|---|---|---|
| **v0.9.0** *(this release)* | 70% in-scope weighted | shipped |
| v0.9.5 | 80% in-scope weighted | structural improvements from production correction streams (real-world bug fixes feeding the next training cycle) |
| v1.0.0 | 85% in-scope weighted | production ship gate — model is reliable for shipped work |
| v2.0.0 | 85%+ | Drupal JSON:API + DaisyUI + full ecosystem coverage |

The version numbering is intentional. 70% does not deserve a 1.0 label
even when the spot exam is perfect, and the public commitment to a
higher bar is the credibility play.

## Run history & lessons learned

This is what it took to get to v0.9.0. Published here in full because
the failures are as instructive as the successes.

- **v1.0 baseline.** 1,267 training entries. **88.5%** on the 30Q spot
  exam. Strong baseline, but the 204Q didn't exist yet, so we didn't
  know about the long tail of cross-tier issues.
- **v1.1 – v1.4. Four failed iterations.** Causes:
  - **Poisoned teacher prompts.** Including a "patterns to avoid" or
    rule-prohibition list in the synthetic-data generation prompt
    teaches the model to *produce* those patterns — the prohibited
    text gets memorized as content. Rule: positive specs and acceptance
    gates, never negative lists.
  - **Stripped `<think>` blocks.** Removing chain-of-thought from training
    answers removed reasoning scaffolding the model needed at inference.
    Restored in v1.5.
  - **Ollama chat template mismatch.** The default Ollama template for
    Qwen3-Coder swallowed system prompts and clipped the assistant
    turn boundary. Manifested as mode collapse during exam runs. Fixed
    by overriding `TEMPLATE` in the Modelfile (the override shipped with
    this release).
  - **Cross-tier dataset bleed.** Topic 11 examples leaking patterns into
    Topic 3 answers. Caught and filtered in the v1.5 corpus.
- **v1.5 — this release.** 1,508 entries, full regeneration with an 80B
  teacher model and the official `llms.txt` files as the only grounding
  context (no hand-curated bundles, no cross-topic mixing). Restored
  `<think>` reasoning. Fixed the chat template. **30Q 100% / 204Q 70.11%.**
  Shipped as v0.9.0.
- **v1.6 / v1.6.1.** Targeted patches (small batches: 18 conversion
  examples + 3 echo-trap fixes) to lift specific tier scores. Result:
  rescored 204Q moved within statistical noise (v1.5 70.11% → v1.6
  69.80% → v1.6.1 69.62%). The targeted echo-trap fix *did* land at
  the model-output level, but a 21-entry patch cannot move a 1,500+
  entry corpus aggregate. Confirmed: the next score lift is structural,
  not incremental. We reverted to v1.5 for the v0.9.0 release.

**Lessons that generalize:**

- Qwen3-Coder-14B has strong Svelte 4 priors that fight Svelte 5
  training. A meaningful share of v1.5's gains is just preventing the
  base model from regressing to Svelte 4 idioms.
- Small targeted patches do not move large-corpus weighted scores.
  Either commit to a substantial (200+ entry) targeted batch with
  diverse tier coverage, or accept that the score won't move. Don't
  repeat the "small patch will fix it" pattern.
- Grader correctness matters as much as model correctness. We rescored
  the 204Q once (`<think>` strip + comment strip) and the score moved
  from 38% raw to 70% rescored — same model, same outputs, different
  grader. The model didn't get worse, the grader got more honest.
- ChatML template override in the Modelfile is mandatory for Qwen3
  base. The default template is wrong for instruction-tuned chat use.

## How Svelte Coder improves over time

Svelte Coder is used by rockypod to ship real Svelte projects. When the
model produces output that needs correction in production, the corrected
version goes into the next training cycle as a high-priority example.
Every release reflects accumulated corrections from real-world usage,
not just synthetic teacher-model output.

This is the difference between a model trained once and a model that
gets better with each cycle of use. v0.9.5 will reflect the corrections
collected between v0.9.0 and that release; v1.0.0 will reflect the
corrections after that.

If you find a specific Svelte 5 / SvelteKit 2 task where the model is
wrong, open an issue on the GitHub repo with the prompt and the correct
answer. That goes into the correction stream.

## Usage

### Ollama (any platform)

```bash
ollama pull rockypod/svelte-coder
ollama run rockypod/svelte-coder "Write a Svelte 5 rune-based useLocalStorage utility with TypeScript generics"
```

The pulled tag includes the production Modelfile (ChatML template, system
prompt, num_predict 1500, repeat_penalty 1.5).

### LM Studio (macOS / Windows)

Download `svelte-coder-v0.9.0-q4_k_m.gguf` from the HuggingFace repo.
In LM Studio, set:

- Prompt template: ChatML
- System prompt: `You are SvelteCoder, an expert Svelte 5 / SvelteKit 2 coding assistant. Answer the question with complete, production-quality code.`
- Temperature: 0.2
- Repeat penalty: 1.5
- Context length: 8192
- Max tokens: 1500

See `integration/lm_studio.md` for full setup.

### Apple Silicon (mlx_lm)

```bash
pip install mlx-lm
mlx_lm.server --model rockypod/svelte-coder --port 8081
```

The HuggingFace repo includes a 4-bit MLX build (`mlx-v0.9.0/`).
Point `--model` at the repo or at the local directory after download.

### Continue.dev (VS Code / JetBrains)

See `integration/continue_dev.json` — drop it into your `~/.continue/`
config and edit the model path to point at your Ollama or LM Studio
endpoint.

### Zed

See `integration/zed.md`.

### llama.cpp

```bash
./llama-cli -m svelte-coder-v0.9.0-q4_k_m.gguf -ngl 99 --temp 0.2 \
  --repeat-penalty 1.5 -c 8192 -n 1500 \
  -p "<|im_start|>system
You are SvelteCoder, an expert Svelte 5 / SvelteKit 2 coding assistant. Answer the question with complete, production-quality code.<|im_end|>
<|im_start|>user
Your question<|im_end|>
<|im_start|>assistant
<think>"
```

## Sizes available

- **14B — recommended default.** Q4_K_M GGUF (~8.4 GB), 4-bit MLX
  (~8.3 GB). Best benchmark across both instruments (100% 30Q, 70.11%
  204Q rescored). Trained on Qwen3-Coder-14B base. Use this if the
  hardware fits.
- **8B** — Q4_K_M GGUF (~5 GB). Trained on Qwen3-8B base (non-coder).
  82.8% 30Q, 74.68% 204Q rescored. Use this when 14B doesn't fit.
- **4B** — Q4_K_M GGUF (~3 GB). Trained on Qwen3-4B base (non-coder).
  79.3% 30Q, 67.81% 204Q rescored. Trades capability for accessibility
  on edge hardware.

All three sizes share the same v1.5 dataset and the same Modelfile
template; they differ in base parameter count and base-model family
(14B uses Qwen3-Coder-14B; 8B/4B use the non-coder Qwen3 line because
no Qwen3-Coder release exists below 14B).

## Limitations

Honest list. If you're considering using this model in shipped code,
read this first.

- **T6 hard reasoning at 41%** on the strict 204Q grader. Multi-step
  refactors with subtle regressions still trip the model. Review hard-
  reasoning output carefully; do not auto-merge.
- **Form-action drift.** The model occasionally reaches for non-canonical
  patterns on form actions (libraries instead of the native action
  triplet). Spec the canonical pattern explicitly in the prompt if you
  hit this.
- **Svelte 4 echo-trap on fix-this-snippet questions.** When asked to
  modernize a Svelte 4 snippet, the model regresses to Svelte 4 idioms
  in roughly 1 in 20 conversions on the 14B. Mitigation: explicit "use
  runes, no `let` reactives, no `on:click`" in the prompt.
- **Smaller-variant Svelte 4 leakage is more frequent.** The 8B and 4B
  variants show occasional Svelte 4 pattern leakage on fix-this-snippet
  questions (`export let`, `on:click`, `<slot>`) — particularly on T1
  (Runes) and T13 (DaisyUI). Users converting Svelte 4 components on
  the smaller variants should review output for these patterns. The
  14B has more capacity to override the base model's pretrained Svelte 4
  reflexes and shows this issue less frequently. Improving smaller
  variants on this axis is a v0.9.5 / v1.0.0 target.
- **No Drupal / DaisyUI coverage.** Both deferred to v2.0.
- **No JS/TS general-purpose coverage outside the Svelte stack.** This
  is a specialist model; for non-Svelte code, use a general code model.

## Score detail

Full per-question results live under `exam/results/` for both the 30Q
and 204Q exams. Tier breakdown for the 204Q (rescored):

| Tier | Topic | Score |
|---|---|---|
| T1 | Svelte 5 runes | 82.4% |
| T2 | SvelteKit routing | 73% |
| T3 | Form actions / enhance | 71% |
| T4 | WCAG / ARIA | 78% |
| T5 | Tailwind v4 | 80% |
| T6 | Hard reasoning | 41% |
| T7 | Postgres + Redis | 72% |
| T8 | D3 dataviz | 68% |
| T9 | Playwright | 70% |
| T11 | Svelte Flow | 75% |
| T12 | Paraglide i18n | 76% |
| **Weighted total** | | **70.11%** |

T1 is the largest weighted tier and the one that matters most for
day-to-day work — 82.4% there is the load-bearing number.

## License & Attribution

**This project is licensed under the MIT License** — see [LICENSE](LICENSE)
for the fine-tuning work, training scripts, and derivative artifacts.

**Base models and teacher model are licensed under Apache 2.0** — see
[LICENSE-APACHE](LICENSE-APACHE) and [NOTICE](NOTICE) for upstream
attribution. Specifically:

- Base models: [Qwen3-Coder-14B](https://huggingface.co/Qwen/Qwen3-Coder-14B),
  [Qwen3-8B](https://huggingface.co/Qwen/Qwen3-8B),
  [Qwen3-4B](https://huggingface.co/Qwen/Qwen3-4B) — © Alibaba Cloud
- Teacher: [Qwen3-Coder-Next 80B](https://huggingface.co/Qwen/Qwen3-Coder-Next)
  — © Alibaba Cloud

The Svelte Coder model weights are derivative works of these Apache 2.0
base models, fine-tuned via LoRA adapters on a proprietary specialist
dataset. Per Apache 2.0 requirements, this NOTICE and the upstream
license text are included in this distribution.

The MIT license aligns with the broader Svelte ecosystem (Svelte,
SvelteKit, and most ecosystem libraries are MIT). Use the weights,
modify them, ship them in commercial products. Attribution appreciated,
not required for the fine-tuning work; required by Apache 2.0 for the
upstream base/teacher model attribution above.

## Links

- HuggingFace: https://huggingface.co/rockypod/svelte-coder
- Ollama: https://ollama.com/rockypod/svelte-coder
- GitHub: https://github.com/rockypod/svelte-coder
- Maintainer: rockypod — homelab Rocky Linux GPU server, Washington State

Built with Unsloth, Qwen3, llama.cpp, mlx_lm, and a lot of correction
streams.
