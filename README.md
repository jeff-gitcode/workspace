# litellm-demo

A demo repository showing [OpenCode](https://opencode.ai) + [LiteLLM](https://docs.litellm.ai) running together in a dev container. Users get OpenCode pre-configured to proxy all major LLM providers through LiteLLM — with just a `.env` file.

**Recommended: Get a free [Groq API key](https://console.groq.com)** (email only, no credit card) for fast responses out of the box. Ollama is available as a local fallback but is slow on CPU-only machines.

## What's Included

- **OpenCode** — AI coding agent, auto-launches on container attach
- **LiteLLM** — Proxies OpenAI, Anthropic, Gemini, Mistral, Groq, Cohere, Together AI, Perplexity, xAI, DeepSeek, and Ollama
- **Ollama** — Runs `qwen2.5-coder:1.5b` locally, no API key required
- **LiteLLM Dashboard** — accessible at `http://localhost:4000` from your browser
- VS Code extensions: GitHub Copilot, GitHub Copilot Chat, OpenCode

## Prerequisites

- [Docker](https://www.docker.com/products/docker-desktop)
- [VS Code](https://code.visualstudio.com/) with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

## Getting Started

### 1. Copy the env file

```bash
cp .env.example .env
```

The file must exist (even if empty) or Docker Compose will fail to start.

**For fast responses:** open `.env` and add your Groq key:

```
GROQ_API_KEY=gsk_...
```

Get one free at [console.groq.com](https://console.groq.com). Without a key, OpenCode falls back to Ollama — which works but is slow (~60–100s per response on CPU-only hardware).

### 2. Open in Dev Container

Open this folder in VS Code, then when prompted click **Reopen in Container** — or open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and run:

```
Dev Containers: Reopen in Container
```

The dev container, LiteLLM, and Ollama all start automatically. `qwen2.5-coder:1.5b` is pulled on first start (~986 MB — this may take a few minutes). OpenCode launches when VS Code attaches.

### 3. Start coding

OpenCode defaults to **Llama 4 Scout via Groq** if `GROQ_API_KEY` is set, otherwise falls back to Ollama. Switch models anytime with the `/model` command.

The LiteLLM dashboard is available at [http://localhost:4000](http://localhost:4000).

## Models

| Model | Provider | Requires | Speed |
|---|---|---|---|
| `llama-4-scout` (default) | Groq | Free API key | Fast |
| `llama-3.3-70b` | Groq | Free API key | Fast |
| `ollama/qwen2.5-coder:1.5b` | Ollama | Nothing | Slow on CPU |
| `github-copilot/gpt-4o` (see Copilot section) | GitHub Copilot | Copilot subscription | Fast |
| `gpt-4o`, `claude-3-5-sonnet`, etc. | Various | Paid API key | Fast |

## GitHub Copilot

GitHub Copilot is available as an additional provider if you have a Copilot subscription (paid, or free for [students and OSS maintainers](https://github.com/features/copilot/plans)).

### Authenticate

Run this once inside the container terminal:

```bash
opencode auth github-copilot
```

Follow the OAuth prompt in your browser. After authenticating, Copilot models appear in the `/model` picker in OpenCode.

### Available Copilot Models

After authenticating, OpenCode shows the full list. Common options include `github-copilot/gpt-4o`, `github-copilot/claude-3-5-sonnet`, and `github-copilot/o3-mini`.

The default model remains **Llama 4 Scout via Groq**. Switch to a Copilot model anytime with `/model`.

## Testing the Setup

### 1. Verify LiteLLM is up

In the container terminal:

```bash
curl http://litellm:4000/health -H "Authorization: Bearer sk-litellm-demo"
```

Expected: `{"status": "healthy"}`

### 2. Test in OpenCode

Type `hi` — if Groq is configured you should get a response in under 2 seconds.

### 3. Verify Ollama (optional)

```bash
curl http://ollama:11434/api/tags
```

Expected: JSON listing `qwen2.5-coder:1.5b`.

### 4. Check the LiteLLM dashboard

Open [http://localhost:4000](http://localhost:4000) in your browser to see request logs.

## Why is Ollama slow?

OpenCode sends a ~9,500-token system prompt with every message. On CPU-only Docker, Ollama processes ~100 tokens/second during the prefill phase — meaning ~95 seconds of silence before the first word appears. With a GPU it's 5–10x faster. Or just use Groq — it's free.

## Project Structure

```
.devcontainer/
  devcontainer.json       # Dev container configuration
  docker-compose.yml      # Defines devcontainer + litellm + ollama services
  litellm-config.yaml     # LiteLLM model definitions
.env.example              # Copy to .env and add your API keys
opencode.json             # OpenCode pre-configured for LiteLLM
```
