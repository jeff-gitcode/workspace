# litellm-demo

A demo repository showing [OpenCode](https://opencode.ai) + [LiteLLM](https://docs.litellm.ai) running together in a dev container. Users get OpenCode pre-configured to proxy all major LLM providers through LiteLLM — with just a `.env` file.

**No API key? No problem.** Ollama runs locally inside the container with `llama3.2` available out of the box.

## What's Included

- **OpenCode** — AI coding agent, auto-launches on container attach
- **LiteLLM** — Proxies OpenAI, Anthropic, Gemini, Mistral, Groq, Cohere, Together AI, Perplexity, xAI, DeepSeek, AWS Bedrock, Azure OpenAI, and Ollama
- **Ollama** — Runs `llama3.2` locally, no API key required
- **LiteLLM Dashboard** — accessible at `http://localhost:4000` from your browser
- VS Code extensions: GitHub Copilot, GitHub Copilot Chat, OpenCode

## Prerequisites

- [Docker](https://www.docker.com/products/docker-desktop)
- [VS Code](https://code.visualstudio.com/) with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

## Getting Started

### 1. Add your API keys (optional)

```bash
cp .env.example .env
```

Open `.env` and fill in keys for any providers you have. If you have none, Ollama with `llama3.2` works out of the box — no keys needed.

### 2. Open in Dev Container

Open this folder in VS Code, then when prompted click **Reopen in Container** — or open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and run:

```
Dev Containers: Reopen in Container
```

The dev container, LiteLLM, and Ollama all start automatically. `llama3.2` is pulled on first start (this may take a few minutes). OpenCode launches when VS Code attaches.

### 3. Start coding

OpenCode is pre-configured to use LiteLLM. Select any model from your configured providers — or pick **Llama 3.2 (Ollama)** for a fully local, free option.

The LiteLLM dashboard is available at [http://localhost:4000](http://localhost:4000).

## Testing the Setup

After the dev container is running and OpenCode has launched:

### 1. Verify LiteLLM is up

In the container terminal:

```bash
curl http://litellm:4000/health
```

Expected: `{"status": "healthy"}`

### 2. Verify Ollama pulled llama3.2

```bash
curl http://ollama:11434/api/tags
```

Expected: JSON response listing `llama3.2`

### 3. Test a model in OpenCode

1. Press `/` and run `/model` to see available models
2. Select `ollama/llama3.2`
3. Type `say hello` — a response confirms Ollama is working

### 4. Test a cloud provider (optional)

1. Make sure your provider key is set in `.env` (e.g. `OPENAI_API_KEY`)
2. Switch model to `gpt-4o-mini` via `/model`
3. Send a prompt — a response confirms LiteLLM is proxying correctly

### 5. Check the LiteLLM dashboard

Open [http://localhost:4000](http://localhost:4000) in your browser to see request logs.

> **Note:** `llama3.2` is pulled on first container start and may take a few minutes. If OpenCode returns an error on that model right after startup, wait a moment and retry.

## Project Structure

```
.devcontainer/
  devcontainer.json       # Dev container configuration
  docker-compose.yml      # Defines devcontainer + litellm + ollama services
  litellm-config.yaml     # LiteLLM model definitions
.env.example              # Copy to .env and add your API keys (optional)
opencode.json             # OpenCode pre-configured for LiteLLM
docs/
  superpowers/
    specs/                # Design documents
    plans/                # Implementation plans
```
