# LiteLLM + OpenCode Docker Compose Design

**Date:** 2026-05-10
**Topic:** Integrate LiteLLM as AI provider in the dev container

## Goal

Give demo users a zero-install experience: clone the repo, add API keys to `.env`, open in VS Code dev container, and OpenCode is ready with LiteLLM proxying all major LLM providers.

## Approach

Docker Compose with two services sharing a network:

- **devcontainer** — Node.js 22, OpenCode installed and auto-launching, pre-configured to point at LiteLLM
- **litellm** — Official LiteLLM proxy image, reads keys from `.env`, exposes dashboard on port 4000

## Architecture

```
┌─────────────────────────────┐     ┌─────────────────────────┐
│  devcontainer (Node.js 22)  │────▶│  litellm (port 4000)    │
│  OpenCode auto-launches     │     │  proxies all providers  │
│  pre-configured base URL    │     │  dashboard on :4000     │
└─────────────────────────────┘     └─────────────────────────┘
         reads .env                          reads .env
```

## Files

| File | Purpose |
|------|---------|
| `.devcontainer/devcontainer.json` | Updated to reference `docker-compose.yml` |
| `.devcontainer/docker-compose.yml` | Defines both services |
| `.devcontainer/litellm-config.yaml` | LiteLLM model definitions (all providers, keys from env) |
| `.env.example` | Template with all provider key names, empty values |
| `.gitignore` | Ensures `.env` is never committed |

## Key Behaviours

- User copies `.env.example` → `.env`, fills in whichever keys they have
- LiteLLM only activates models for keys that are non-empty
- OpenCode configured via `opencode.json` with `baseURL: http://litellm:4000` and `provider: openai` (LiteLLM speaks OpenAI-compatible API)
- LiteLLM dashboard accessible at `http://localhost:4000` from the host browser
- If `.env` is missing or all keys empty, LiteLLM starts but returns errors — user gets a clear message

## Providers Covered

OpenAI, Anthropic, Google Gemini, Azure OpenAI, AWS Bedrock, Mistral, Cohere, Groq, Together AI, Perplexity, xAI (Grok), DeepSeek, Ollama (local)

## Out of Scope

- Authentication on the LiteLLM dashboard (demo only)
- Persistent LiteLLM database across rebuilds
- Custom model fine-tuning or routing rules
