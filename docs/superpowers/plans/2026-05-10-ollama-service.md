# Ollama Docker Compose Service Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add Ollama as a third Docker Compose service so users get llama3.2 out of the box with no API key required.

**Architecture:** Ollama runs as a Docker Compose service on the shared `litellm-network`, with models persisted in a named volume. LiteLLM points at `http://ollama:11434` directly. The model is pulled automatically on first start via a startup command.

**Tech Stack:** Docker Compose, Ollama (ollama/ollama image), LiteLLM

---

## File Map

| File | Action | Purpose |
|------|--------|---------|
| `.devcontainer/docker-compose.yml` | Modify | Add ollama service + named volume |
| `.devcontainer/litellm-config.yaml` | Modify | Update ollama entry to use hardcoded `http://ollama:11434`, rename to `llama3.2` |
| `.env.example` | Modify | Remove `OLLAMA_API_BASE` |
| `opencode.json` | Modify | Update model name from `ollama/llama3` to `ollama/llama3.2` |
| `README.md` | Modify | Note llama3.2 works out of the box, no API key needed |

---

### Task 1: Update `docker-compose.yml` to add Ollama service

**Files:**
- Modify: `.devcontainer/docker-compose.yml`

- [ ] **Step 1: Replace `.devcontainer/docker-compose.yml` with this content**

```yaml
version: "3.8"

services:
  devcontainer:
    image: mcr.microsoft.com/devcontainers/javascript-node:22
    volumes:
      - ..:/workspace:cached
    command: sleep infinity
    env_file:
      - ../.env
    networks:
      - litellm-network

  litellm:
    image: ghcr.io/berriai/litellm:main-latest
    ports:
      - "4000:4000"
    volumes:
      - ./litellm-config.yaml:/app/config.yaml
    env_file:
      - ../.env
    command: ["--config", "/app/config.yaml", "--port", "4000", "--detailed_debug"]
    depends_on:
      - ollama
    networks:
      - litellm-network

  ollama:
    image: ollama/ollama
    volumes:
      - ollama-data:/root/.ollama
    networks:
      - litellm-network
    entrypoint: ["/bin/sh", "-c", "ollama serve & sleep 5 && ollama pull llama3.2 && wait"]

volumes:
  ollama-data:

networks:
  litellm-network:
    driver: bridge
```

- [ ] **Step 2: Commit**

```bash
git add .devcontainer/docker-compose.yml
git commit -m "feat: add ollama service to docker compose"
```

---

### Task 2: Update `litellm-config.yaml` for Ollama

**Files:**
- Modify: `.devcontainer/litellm-config.yaml`

- [ ] **Step 1: Replace the Ollama entry at the bottom of `.devcontainer/litellm-config.yaml`**

Find this block:

```yaml
  # Ollama (local)
  - model_name: ollama/llama3
    litellm_params:
      model: ollama/llama3
      api_base: os.environ/OLLAMA_API_BASE
```

Replace with:

```yaml
  # Ollama (local - no API key required)
  - model_name: ollama/llama3.2
    litellm_params:
      model: ollama/llama3.2
      api_base: http://ollama:11434
```

- [ ] **Step 2: Commit**

```bash
git add .devcontainer/litellm-config.yaml
git commit -m "feat: update litellm config to use ollama service at http://ollama:11434"
```

---

### Task 3: Remove `OLLAMA_API_BASE` from `.env.example`

**Files:**
- Modify: `.env.example`

- [ ] **Step 1: Remove the Ollama section from `.env.example`**

Find and remove these lines:

```bash
# Ollama (local) - set to your Ollama host if running locally
OLLAMA_API_BASE=
```

- [ ] **Step 2: Commit**

```bash
git add .env.example
git commit -m "chore: remove OLLAMA_API_BASE from .env.example (now internal)"
```

---

### Task 4: Update `opencode.json` model name

**Files:**
- Modify: `opencode.json`

- [ ] **Step 1: Update the Ollama model entry in `opencode.json`**

Find:

```json
"ollama/llama3": { "name": "Llama 3 (Ollama/local)" }
```

Replace with:

```json
"ollama/llama3.2": { "name": "Llama 3.2 (Ollama - no API key required)" }
```

- [ ] **Step 2: Commit**

```bash
git add opencode.json
git commit -m "feat: update opencode.json to use llama3.2 model name"
```

---

### Task 5: Update `README.md`

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Update the What's Included section and Getting Started**

Replace the entire `README.md` with:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: update README to include ollama service and llama3.2"
```
