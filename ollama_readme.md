# Svelte Coder

A **Svelte 5 / SvelteKit 2 specialist** coding model in three sizes.
Free to use under MIT. Built by [rockypod](https://rockypod.com) on a
homelab RTX 3090 Ti using continuous retrieval-augmented fine-tuning
(RAFT).

## Quickstart

```bash
# 14B — recommended default
ollama run rockypod/svelte-coder

# 8B — for hardware where 14B doesn't fit
ollama run rockypod/svelte-coder:8b

# 4B — edge hardware
ollama run rockypod/svelte-coder:4b
```

## Sizes

| Tag | Params | Disk | VRAM | When to pick |
|---|---|---|---|---|
| `:latest` / `:v0.9.0` | 14B | 8.4 GB | ~10 GB | **Recommended.** Best benchmark scores. |
| `:v0.9.0-8b` | 8B | 5.0 GB | ~6 GB | Mid-tier GPUs, 16 GB VRAM laptops. |
| `:v0.9.0-4b` | 4B | 2.5 GB | ~3 GB | Edge devices, entry-level GPUs. |

## Benchmark (v0.9.0)

| Variant | 30Q spot | 204Q in-scope (rescored) |
|---|---|---|
| **14B** *(recommended)* | **100%** | 70.11% |
| 8B | 82.8% | 74.68% |
| 4B | 79.3% | 67.81% |

The 30Q spot exam is the cleaner instrument — pick by 30Q, not 204Q.
The 204Q has known keyword-matching grader artifacts. The "8B beats
14B on 204Q" inversion is grader noise, not real capability — see
the [GitHub repo](https://github.com/rockypod/svelte-coder) for the
full transparency write-up.

## What it does well

- **Svelte 5 runes** — `$state`, `$derived`, `$effect`, `$props`, `$bindable`
- **SvelteKit 2** — `+page.server.ts` actions, `load()`, redirects, error
  handling, route groups, hooks
- **Production patterns** — accessibility (WCAG / ARIA), Playwright
  end-to-end tests, D3 visualizations, Svelte Flow diagrams, DaisyUI
  components

## What it doesn't do well

- **Svelte 4 → Svelte 5 conversion** is weakest on the 4B and weak on
  the 8B. The pretrained Svelte 4 reflexes (`export let`, `on:click`,
  `<slot>`) leak through more often on smaller variants. Use the 14B
  for Svelte 4 conversion work.
- **Multi-step architectural reasoning** is weaker on the 4B. Use the
  8B or 14B for refactors.

## Production parameters

The Modelfile ships with these defaults — they're load-bearing:

```
PARAMETER temperature 0.2
PARAMETER num_ctx 8192
PARAMETER num_predict 1500
PARAMETER repeat_penalty 1.5
```

Use a chat client that respects the Modelfile (Ollama CLI, Continue,
Zed, LM Studio with the included template). The OpenAI-compatible
`/v1` endpoint silently drops `num_ctx` — use `/api/chat` if you need
to override context length over HTTP.

## License & attribution

- **Fine-tuning work:** MIT — see [LICENSE](https://github.com/rockypod/svelte-coder/blob/main/LICENSE)
- **Base model:** [Qwen3-Coder-14B](https://huggingface.co/Qwen/Qwen3-Coder-14B) /
  [Qwen3-8B](https://huggingface.co/Qwen/Qwen3-8B) /
  [Qwen3-4B](https://huggingface.co/Qwen/Qwen3-4B) — Apache 2.0, © Alibaba Cloud
- **Teacher model (synthetic data):** Qwen3-Coder-Next 80B — Apache 2.0, © Alibaba Cloud
- See [NOTICE](https://github.com/rockypod/svelte-coder/blob/main/NOTICE)
  for the full attribution.

## Links

- [GitHub](https://github.com/rockypod/svelte-coder) — exam, integration guides, transparency notes
- [HuggingFace 14B](https://huggingface.co/rockypod/svelte-coder) (with MLX 4-bit weights for Apple Silicon)
- [HuggingFace 8B](https://huggingface.co/rockypod/svelte-coder-8b)
- [HuggingFace 4B](https://huggingface.co/rockypod/svelte-coder-4b)
