# litellm-demo

A demo repository with a pre-configured dev container that includes [OpenCode](https://opencode.ai) ready to use out of the box.

## Dev Container

This repo includes a `.devcontainer` configuration so any developer on Windows, macOS, or Linux can open it in a consistent, pre-configured environment.

**What's included:**

- Node.js 22 LTS
- npm, git, zsh + oh-my-zsh
- [OpenCode](https://opencode.ai) (installed globally via `opencode-ai`)
- VS Code extensions: GitHub Copilot, GitHub Copilot Chat, OpenCode (`sst-dev.opencode`)
- OpenCode launches automatically when you attach to the container

## Getting Started

### Prerequisites

- [Docker](https://www.docker.com/products/docker-desktop)
- [VS Code](https://code.visualstudio.com/) with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

### Open in Dev Container

1. Clone this repository and open the folder in VS Code.
2. When prompted, click **Reopen in Container** — or open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and run:

   ```
   Dev Containers: Reopen in Container
   ```

3. Wait for the container to build and for `postCreateCommand` to install OpenCode.

### Verify the setup

In the container terminal:

```bash
node --version    # v22.x.x
opencode --version  # 0.x.x
```

### Configure OpenCode

API keys are set per-user at runtime. Run `opencode` and follow the prompts to configure your AI provider credentials.

## Project Structure

```
.devcontainer/
  devcontainer.json   # Dev container configuration
docs/
  superpowers/
    specs/            # Design documents
    plans/            # Implementation plans
```
