# OpenCode + LiteLLM Demo

## What Is This?

A ready-to-run dev container that wires **OpenCode** (AI coding agent) to **LiteLLM** (unified LLM proxy) — giving you access to 14+ models from a single endpoint, with just a `.env` file.

---

## Architecture

```
OpenCode → LiteLLM proxy (http://litellm:4000/v1) → Provider API
```

LiteLLM presents an OpenAI-compatible API regardless of the upstream provider. OpenCode talks only to LiteLLM.

---

## Services

| Service | Purpose | Port |
|---|---|---|
| `litellm` | Unified proxy | 4000 |
| `ollama` | Local model runner | 11434 (internal) |
| `devcontainer` | Node 22 dev environment | — |

---

## Models at a Glance

| Model | Provider | Key Required |
|---|---|---|
| `llama-4-scout` (default) | Groq | Free |
| `llama-3.3-70b` | Groq | Free |
| `gpt-4o`, `gpt-4o-mini` | OpenAI | Paid |
| `claude-3-5-sonnet`, `claude-3-5-haiku` | Anthropic | Paid |
| `gemini-2.0-flash`, `gemini-1.5-pro` | Google | Paid |
| `mistral-large` | Mistral | Paid |
| `deepseek-chat` | DeepSeek | Paid |
| `ollama/qwen2.5-coder:1.5b` | Ollama (local) | None |

Switch models anytime inside OpenCode with `/model`.

---

## Quick Start

```bash
# 1. Copy env file
cp .env.example .env

# 2. Add a free Groq key (optional but recommended)
echo "GROQ_API_KEY=gsk_..." >> .env

# 3. Reopen in Dev Container (VS Code)
# Ctrl+Shift+P → Dev Containers: Reopen in Container
```

OpenCode launches automatically when VS Code attaches.

---

## Key Features

- **Zero config** — works out of the box with Ollama (no API key needed)
- **Free fast option** — Groq API key is free, no credit card required
- **GitHub Copilot** — authenticate once with `opencode auth github-copilot`
- **MD2Card MCP** — convert Markdown to visual knowledge cards
- **LiteLLM Dashboard** — view request logs at `http://localhost:4000`
- **`drop_params: true`** — silently drops unsupported params per provider, preventing errors

---

## Health Check

```bash
curl http://litellm:4000/health -H "Authorization: Bearer sk-litellm-demo"
# → {"status": "healthy"}
```
