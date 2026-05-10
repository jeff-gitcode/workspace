# litellm-demo

A demo repository showing [OpenCode](https://opencode.ai) + [LiteLLM](https://docs.litellm.ai) running together in a dev container. Users get OpenCode pre-configured to proxy all major LLM providers through LiteLLM — with just a `.env` file.

## What's Included

- **OpenCode** — AI coding agent, auto-launches on container attach
- **LiteLLM** — Proxies OpenAI, Anthropic, Gemini, Mistral, Groq, Cohere, Together AI, Perplexity, xAI, DeepSeek, AWS Bedrock, Azure OpenAI, and Ollama
- **LiteLLM Dashboard** — accessible at `http://localhost:4000` from your browser
- VS Code extensions: GitHub Copilot, GitHub Copilot Chat, OpenCode

## Prerequisites

- [Docker](https://www.docker.com/products/docker-desktop)
- [VS Code](https://code.visualstudio.com/) with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

## Getting Started

### 1. Add your API keys

```bash
cp .env.example .env
```

Open `.env` and fill in the keys for whichever providers you have. You only need at least one.

### 2. Open in Dev Container

Open this folder in VS Code, then when prompted click **Reopen in Container** — or open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and run:

```
Dev Containers: Reopen in Container
```

Both the dev container and LiteLLM start automatically. OpenCode launches when VS Code attaches.

### 3. Start coding

OpenCode is pre-configured to use LiteLLM. Select any model from your configured providers and start coding.

The LiteLLM dashboard is available at [http://localhost:4000](http://localhost:4000).

## Project Structure

```
.devcontainer/
  devcontainer.json       # Dev container configuration
  docker-compose.yml      # Defines devcontainer + litellm services
  litellm-config.yaml     # LiteLLM model definitions
.env.example              # Copy to .env and add your API keys
opencode.json             # OpenCode pre-configured for LiteLLM
docs/
  superpowers/
    specs/                # Design documents
    plans/                # Implementation plans
```
