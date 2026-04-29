# LM Studio Setup

1. Download `svelte-coder-v0.9.0-q4_k_m.gguf` from
   [HuggingFace](https://huggingface.co/rockypod/svelte-coder).
2. Load it in LM Studio.
3. Set the prompt template:

| Field | Value |
|---|---|
| Before System | `<|im_start|>system` |
| After System | `<|im_end|>` |
| Before User | `<|im_start|>user` |
| After User | `<|im_end|>` |
| Before Assistant | `<|im_start|>assistant\n<think>` |

The trailing `<think>` on the assistant prefix primes the model's
chain-of-thought block — the v1.5 training corpus has `<think>...</think>`
reasoning at the start of every answer, and the inference template needs
to match.

4. Recommended settings:

| Setting | Value |
|---|---|
| System prompt | `You are SvelteCoder, an expert Svelte 5 / SvelteKit 2 coding assistant. Answer the question with complete, production-quality code.` |
| Temperature | 0.2 |
| Repeat penalty | 1.5 |
| Context length | 8192 |
| Max tokens | 1500 |

`repeat_penalty 1.5` is the production-tested value — anything lower
caused mode collapse on long answers in v1.4 testing.
