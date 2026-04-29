# Zed Editor Setup

Add to `~/.config/zed/settings.json`:

```json
{
  "language_models": {
    "openai": {
      "api_url": "http://localhost:8081/v1",
      "available_models": [
        {
          "name": "svelte-coder-v0.9.0-mlx",
          "display_name": "Svelte Coder v0.9.0",
          "max_tokens": 8192
        }
      ]
    }
  },
  "assistant": {
    "default_model": {
      "provider": "openai",
      "model": "svelte-coder-v0.9.0-mlx"
    }
  }
}
```

Start the MLX server first:

```bash
mlx_lm.server --model rockypod/svelte-coder --port 8081
```

Or, if you've downloaded the MLX directory locally, point `--model` at
the local path.
