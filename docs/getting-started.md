# Getting started

How to set up a machine to work on this project. Tools are added here as each
part of the project needs them.

## Tools to install

| Tool | Why | Check it works |
|---|---|---|
| VS Code | Editor | `code --version` |
| Git | Version control | `git --version` |
| GitHub CLI | Repos and pull requests from the terminal | `gh --version`, then `gh auth login` |
| uv | Python versions, virtual environments and dependencies | `uv --version` |
| Python 3.12+ | Backend language, installed through uv | `uv python install 3.12` |
| Node.js (LTS) | Runs the TypeScript tooling | `node --version` |
| pnpm | JavaScript package manager for the workspace | `npm install -g pnpm`, then `pnpm --version` |
| Docker Desktop | Runs Postgres locally | `docker --version` |

## First-time Git setup

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

## Opening the project

1. Open the folder in VS Code: `code personal-library`.
2. Accept the prompt to install the recommended extensions.
3. Open the Claude Code panel and sign in.

## Commands

Backend, web app and extension commands will be added here as each is set up.
