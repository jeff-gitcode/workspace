# LiteLLM + OpenCode Docker Compose Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add LiteLLM as a Docker Compose service alongside the dev container so users can use any LLM provider through OpenCode with just a `.env` file.

**Architecture:** Docker Compose defines two services (`devcontainer` and `litellm`) on a shared network. LiteLLM reads API keys from `.env` and proxies all major providers via an OpenAI-compatible API on port 4000. OpenCode is pre-configured to point at `http://litellm:4000`. Users copy `.env.example` to `.env` and fill in whichever keys they have.

**Tech Stack:** Docker Compose, LiteLLM (ghcr.io/berriai/litellm), Node.js 22 dev container, OpenCode

---

## File Map

| File | Action | Purpose |
|------|--------|---------|
| `.devcontainer/docker-compose.yml` | Create | Defines devcontainer + litellm services |
| `.devcontainer/devcontainer.json` | Modify | Reference docker-compose.yml, remove postAttachCommand conflict |
| `.devcontainer/litellm-config.yaml` | Create | LiteLLM model list for all providers |
| `opencode.json` | Create | Pre-configure OpenCode to use LiteLLM |
| `.env.example` | Create | Template with all provider key names |
| `.gitignore` | Create | Ignore `.env` |
| `README.md` | Modify | Update getting started instructions |

---

### Task 1: Create `.gitignore`

**Files:**
- Create: `.gitignore`

- [ ] **Step 1: Create `.gitignore`**

Create `.gitignore` at the repo root with this content:

```
.env
```

- [ ] **Step 2: Commit**

```bash
git add .gitignore
git commit -m "chore: add gitignore to exclude .env"
```

---

### Task 2: Create `.env.example`

**Files:**
- Create: `.env.example`

- [ ] **Step 1: Create `.env.example`**

Create `.env.example` at the repo root:

```bash
# Copy this file to .env and fill in the keys for providers you have.
# LiteLLM will only activate models for keys that are set.

# OpenAI
OPENAI_API_KEY=

# Anthropic
ANTHROPIC_API_KEY=

# Google Gemini
GEMINI_API_KEY=

# Azure OpenAI
AZURE_API_KEY=
AZURE_API_BASE=
AZURE_API_VERSION=

# AWS Bedrock
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_REGION_NAME=

# Mistral
MISTRAL_API_KEY=

# Cohere
COHERE_API_KEY=

# Groq
GROQ_API_KEY=

# Together AI
TOGETHERAI_API_KEY=

# Perplexity
PERPLEXITYAI_API_KEY=

# xAI (Grok)
XAI_API_KEY=

# DeepSeek
DEEPSEEK_API_KEY=

# Ollama (local) - set to your Ollama host if running locally
OLLAMA_API_BASE=
```

- [ ] **Step 2: Commit**

```bash
git add .env.example
git commit -m "chore: add .env.example with all provider keys"
```

---

### Task 3: Create LiteLLM config

**Files:**
- Create: `.devcontainer/litellm-config.yaml`

- [ ] **Step 1: Create `.devcontainer/litellm-config.yaml`**

```yaml
model_list:
  # OpenAI
  - model_name: gpt-4o
    litellm_params:
      model: openai/gpt-4o
      api_key: os.environ/OPENAI_API_KEY

  - model_name: gpt-4o-mini
    litellm_params:
      model: openai/gpt-4o-mini
      api_key: os.environ/OPENAI_API_KEY

  # Anthropic
  - model_name: claude-3-5-sonnet
    litellm_params:
      model: anthropic/claude-3-5-sonnet-20241022
      api_key: os.environ/ANTHROPIC_API_KEY

  - model_name: claude-3-5-haiku
    litellm_params:
      model: anthropic/claude-3-5-haiku-20241022
      api_key: os.environ/ANTHROPIC_API_KEY

  # Google Gemini
  - model_name: gemini-2.0-flash
    litellm_params:
      model: gemini/gemini-2.0-flash
      api_key: os.environ/GEMINI_API_KEY

  - model_name: gemini-1.5-pro
    litellm_params:
      model: gemini/gemini-1.5-pro
      api_key: os.environ/GEMINI_API_KEY

  # Mistral
  - model_name: mistral-large
    litellm_params:
      model: mistral/mistral-large-latest
      api_key: os.environ/MISTRAL_API_KEY

  # Cohere
  - model_name: command-r-plus
    litellm_params:
      model: cohere/command-r-plus
      api_key: os.environ/COHERE_API_KEY

  # Groq
  - model_name: llama-3.3-70b
    litellm_params:
      model: groq/llama-3.3-70b-versatile
      api_key: os.environ/GROQ_API_KEY

  # Together AI
  - model_name: llama-3.1-405b
    litellm_params:
      model: together_ai/meta-llama/Meta-Llama-3.1-405B-Instruct-Turbo
      api_key: os.environ/TOGETHERAI_API_KEY

  # Perplexity
  - model_name: sonar-pro
    litellm_params:
      model: perplexity/sonar-pro
      api_key: os.environ/PERPLEXITYAI_API_KEY

  # xAI (Grok)
  - model_name: grok-2
    litellm_params:
      model: xai/grok-2
      api_key: os.environ/XAI_API_KEY

  # DeepSeek
  - model_name: deepseek-chat
    litellm_params:
      model: deepseek/deepseek-chat
      api_key: os.environ/DEEPSEEK_API_KEY

  # Ollama (local)
  - model_name: ollama/llama3
    litellm_params:
      model: ollama/llama3
      api_base: os.environ/OLLAMA_API_BASE

litellm_settings:
  drop_params: true
  # Ignore models whose keys are missing
  success_callback: []

general_settings:
  master_key: sk-litellm-demo
```

- [ ] **Step 2: Commit**

```bash
git add .devcontainer/litellm-config.yaml
git commit -m "feat: add litellm config for all providers"
```

---

### Task 4: Create Docker Compose file

**Files:**
- Create: `.devcontainer/docker-compose.yml`

- [ ] **Step 1: Create `.devcontainer/docker-compose.yml`**

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
    networks:
      - litellm-network

networks:
  litellm-network:
    driver: bridge
```

- [ ] **Step 2: Commit**

```bash
git add .devcontainer/docker-compose.yml
git commit -m "feat: add docker compose with litellm service"
```

---

### Task 5: Update `devcontainer.json`

**Files:**
- Modify: `.devcontainer/devcontainer.json`

- [ ] **Step 1: Replace `devcontainer.json` content**

Replace the entire file with:

```json
{
  "name": "litellm-demo",
  "dockerComposeFile": "docker-compose.yml",
  "service": "devcontainer",
  "workspaceFolder": "/workspace",
  "postCreateCommand": "npm install -g opencode-ai",
  "postAttachCommand": "opencode",
  "remoteUser": "node",
  "customizations": {
    "vscode": {
      "extensions": [
        "github.copilot",
        "github.copilot-chat",
        "sst-dev.opencode"
      ]
    }
  }
}
```

- [ ] **Step 2: Commit**

```bash
git add .devcontainer/devcontainer.json
git commit -m "feat: update devcontainer to use docker compose"
```

---

### Task 6: Create `opencode.json`

**Files:**
- Create: `opencode.json`

OpenCode uses `opencode.json` at the repo root for project-level config. We pre-configure it to use LiteLLM as the provider.

- [ ] **Step 1: Create `opencode.json`**

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "litellm": {
      "name": "LiteLLM",
      "baseURL": "http://litellm:4000",
      "api_key": "sk-litellm-demo",
      "models": {
        "gpt-4o": { "name": "GPT-4o (OpenAI)" },
        "gpt-4o-mini": { "name": "GPT-4o Mini (OpenAI)" },
        "claude-3-5-sonnet": { "name": "Claude 3.5 Sonnet (Anthropic)" },
        "claude-3-5-haiku": { "name": "Claude 3.5 Haiku (Anthropic)" },
        "gemini-2.0-flash": { "name": "Gemini 2.0 Flash (Google)" },
        "gemini-1.5-pro": { "name": "Gemini 1.5 Pro (Google)" },
        "mistral-large": { "name": "Mistral Large" },
        "command-r-plus": { "name": "Command R+ (Cohere)" },
        "llama-3.3-70b": { "name": "Llama 3.3 70B (Groq)" },
        "llama-3.1-405b": { "name": "Llama 3.1 405B (Together AI)" },
        "sonar-pro": { "name": "Sonar Pro (Perplexity)" },
        "grok-2": { "name": "Grok 2 (xAI)" },
        "deepseek-chat": { "name": "DeepSeek Chat" },
        "ollama/llama3": { "name": "Llama 3 (Ollama/local)" }
      }
    }
  }
}
```

- [ ] **Step 2: Commit**

```bash
git add opencode.json
git commit -m "feat: add opencode.json pre-configured for litellm"
```

---

### Task 7: Update README

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Replace README content**

Replace the entire `README.md` with:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: update README for litellm + opencode docker compose setup"
```
