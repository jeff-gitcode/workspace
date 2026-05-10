# Dev Container with OpenCode Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a `.devcontainer` configuration so that any developer (on Windows, macOS, or Linux) can open this repo in a dev container with OpenCode pre-installed.

**Architecture:** A single `devcontainer.json` using the Microsoft Node.js 22 dev container image. OpenCode is installed globally via `postCreateCommand`. No custom Dockerfile is needed.

**Tech Stack:** Docker, VS Code Dev Containers, Node.js 22, opencode-ai (npm package)

---

### Task 1: Create the `.devcontainer/devcontainer.json`

**Files:**
- Create: `.devcontainer/devcontainer.json`

- [x] **Step 1: Create `.devcontainer/` directory and `devcontainer.json`**

Create `.devcontainer/devcontainer.json` with this exact content:

```json
{
  "name": "litellm-demo",
  "image": "mcr.microsoft.com/devcontainers/javascript-node:22",
  "postCreateCommand": "npm install -g opencode-ai",
  "remoteUser": "node",
  "customizations": {
    "vscode": {
      "extensions": [
        "github.copilot",
        "github.copilot-chat"
      ]
    }
  }
}
```

- [x] **Step 2: Verify the file exists and is valid JSON**

Run:
```bash
cat .devcontainer/devcontainer.json | python3 -m json.tool
```

Expected output: pretty-printed JSON with no errors.

- [x] **Step 3: Commit**

```bash
git init
git add .devcontainer/devcontainer.json
git commit -m "feat: add dev container with OpenCode"
```

---

### Task 2: Smoke-test the dev container (manual verification)

**Files:** none modified

- [x] **Step 1: Open the repo in VS Code**

Open the `litellm-demo` folder in VS Code.

- [x] **Step 2: Reopen in container**

When VS Code prompts "Reopen in Container", click it. Or open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and run:

> Dev Containers: Reopen in Container

- [x] **Step 3: Verify Node.js is available**

In the container terminal, run:
```bash
node --version
```
Expected: `v22.x.x`

- [x] **Step 4: Verify OpenCode is installed**

In the container terminal, run:
```bash
opencode --version
```
Expected: a version string (e.g., `0.x.x`), confirming OpenCode installed successfully.

- [x] **Step 5: Done**

The dev container is working. No further commits are needed for this task.
