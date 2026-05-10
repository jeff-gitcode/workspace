# Ollama Docker Compose Service Design

**Date:** 2026-05-10
**Topic:** Add Ollama as a third Docker Compose service

## Goal

Add Ollama as a Docker Compose service so users get a free, fully local LLM (llama3.2) out of the box with no API key required.

## Architecture

```
devcontainer  ──▶  litellm:4000  ──▶  ollama:11434
                        │
                   all other providers (via API keys in .env)
```

Three Docker Compose services on a shared network. Ollama runs as its own service, persists models in a named Docker volume so `llama3.2` is only pulled once and survives container rebuilds.

## Changes Required

| File | Change |
|------|--------|
| `.devcontainer/docker-compose.yml` | Add `ollama` service with named volume and model pull on start |
| `.devcontainer/litellm-config.yaml` | Update Ollama entry: hardcode `api_base: http://ollama:11434`, use `llama3.2`, no `api_key` |
| `.env.example` | Remove `OLLAMA_API_BASE` (no longer needed) |
| `opencode.json` | Update model name from `ollama/llama3` to `ollama/llama3.2` |
| `README.md` | Note that Ollama + llama3.2 works out of the box, no API key needed |

## Key Behaviours

- Ollama starts with the other services automatically
- `llama3.2` is pulled on first start via `ollama pull llama3.2` in the service entrypoint
- Model is cached in a named volume (`ollama-data`) — no re-pull on rebuild
- LiteLLM points at `http://ollama:11434` internally — no user configuration needed
- No API key required for Ollama
- `OLLAMA_API_BASE` removed from `.env.example`

## Out of Scope

- GPU support (CPU-only for portability)
- Multiple Ollama models
- Ollama web UI
