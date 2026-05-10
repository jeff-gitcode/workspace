# LiteLLM Configuration

This project uses [LiteLLM](https://docs.litellm.ai) as a unified proxy to route requests from OpenCode to any supported LLM provider via a single OpenAI-compatible endpoint at `http://litellm:4000/v1`.


## How It Works

```
OpenCode → LiteLLM proxy (http://litellm:4000) → Provider API (Groq, OpenAI, Anthropic, etc.)
```

LiteLLM is configured as a custom provider in `opencode.json` using the `@ai-sdk/openai-compatible` npm package. All models share the same base URL and a demo master key (`sk-litellm-demo`).

## Services

Defined in `.devcontainer/docker-compose.yml`:

| Service | Image | Port |
|---|---|---|
| `litellm` | `ghcr.io/berriai/litellm:main-latest` | 4000 |
| `ollama` | `ollama/ollama` | 11434 (internal) |
| `devcontainer` | `mcr.microsoft.com/devcontainers/javascript-node:22` | — |

LiteLLM depends on Ollama being healthy before it starts. Ollama automatically pulls `qwen2.5-coder:1.5b` on first launch (~986 MB).

## Model Configuration

Defined in `.devcontainer/litellm-config.yaml`. Models are only active when their corresponding API key is set in `.env`.

| Model alias | Upstream model | Provider | API key env var |
|---|---|---|---|
| `llama-4-scout` | `groq/meta-llama/llama-4-scout-17b-16e-instruct` | Groq | `GROQ_API_KEY` |
| `llama-3.3-70b` | `groq/llama-3.3-70b-versatile` | Groq | `GROQ_API_KEY` |
| `gpt-4o` | `openai/gpt-4o` | OpenAI | `OPENAI_API_KEY` |
| `gpt-4o-mini` | `openai/gpt-4o-mini` | OpenAI | `OPENAI_API_KEY` |
| `claude-3-5-sonnet` | `anthropic/claude-3-5-sonnet-20241022` | Anthropic | `ANTHROPIC_API_KEY` |
| `claude-3-5-haiku` | `anthropic/claude-3-5-haiku-20241022` | Anthropic | `ANTHROPIC_API_KEY` |
| `gemini-2.0-flash` | `gemini/gemini-2.0-flash` | Google | `GEMINI_API_KEY` |
| `gemini-1.5-pro` | `gemini/gemini-1.5-pro` | Google | `GEMINI_API_KEY` |
| `mistral-large` | `mistral/mistral-large-latest` | Mistral | `MISTRAL_API_KEY` |
| `command-r-plus` | `cohere/command-r-plus` | Cohere | `COHERE_API_KEY` |
| `llama-3.1-405b` | `together_ai/meta-llama/Meta-Llama-3.1-405B-Instruct-Turbo` | Together AI | `TOGETHERAI_API_KEY` |
| `sonar-pro` | `perplexity/sonar-pro` | Perplexity | `PERPLEXITYAI_API_KEY` |
| `grok-2` | `xai/grok-2` | xAI | `XAI_API_KEY` |
| `deepseek-chat` | `deepseek/deepseek-chat` | DeepSeek | `DEEPSEEK_API_KEY` |
| `ollama/qwen2.5-coder:1.5b` | `ollama_chat/qwen2.5-coder:1.5b` | Ollama (local) | none |
| `ollama/llama3.1:8b` | `ollama_chat/llama3.1:8b` | Ollama (local) | none |

All Ollama models run locally via `http://ollama:11434` with a 16384-token context window.

## Global LiteLLM Settings

```yaml
litellm_settings:
  drop_params: true   # silently drops unsupported params per provider

general_settings:
  master_key: sk-litellm-demo
```

`drop_params: true` prevents errors when OpenCode passes parameters a given provider does not support (e.g. `tool_choice` on Ollama).

## OpenCode Integration

In `opencode.json`, LiteLLM is registered as a provider:

```json
"provider": {
  "litellm": {
    "npm": "@ai-sdk/openai-compatible",
    "options": {
      "baseURL": "http://litellm:4000/v1",
      "apiKey": "sk-litellm-demo"
    }
  }
}
```

The default model is `litellm/llama-4-scout`. Switch models at any time with the `/model` command inside OpenCode.

## Dashboard

The LiteLLM dashboard is available at [http://localhost:4000](http://localhost:4000) while the dev container is running. It shows request logs, model usage, and latency.

## Health Check

```bash
curl http://litellm:4000/health -H "Authorization: Bearer sk-litellm-demo"
# Expected: {"status": "healthy"}
```

## Adding a New Model

1. Add the model entry to `.devcontainer/litellm-config.yaml`:

```yaml
- model_name: my-model
  litellm_params:
    model: provider/model-id
    api_key: os.environ/MY_PROVIDER_API_KEY
```

2. Add the corresponding key to `.env.example` and `.env`.

3. Register the model alias in `opencode.json` under `provider.litellm.models`:

```json
"my-model": { "name": "My Model (Provider)" }
```

4. Rebuild the dev container (`Dev Containers: Rebuild Container`).
