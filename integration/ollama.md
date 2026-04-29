# Ollama Setup

The simplest path — pull the published tag:

```bash
ollama pull rockypod/svelte-coder
ollama run rockypod/svelte-coder "Write a Svelte 5 rune-based useLocalStorage utility"
```

The published tag includes the production Modelfile (ChatML template,
baked system prompt, `temperature 0.2`, `num_predict 1500`,
`repeat_penalty 1.5`, `num_ctx 8192`).

## Self-host from GGUF

If you prefer to manage the GGUF locally:

1. Download `svelte-coder-v0.9.0-q4_k_m.gguf` from
   [HuggingFace](https://huggingface.co/rockypod/svelte-coder).
2. Use the [`Modelfile`](../Modelfile) in this repo (it sets the correct
   ChatML template and inference parameters):

   ```bash
   ollama create svelte-coder -f Modelfile
   ollama run svelte-coder
   ```

The `TEMPLATE` block in the Modelfile is **load-bearing** — Ollama's
default Qwen3 template clips the assistant turn boundary and swallows
the system prompt, which caused mode collapse during v1.4 testing. Do
not strip the override.
