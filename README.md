# 🛡️ PR Sentinel — Autonomous PR Pipeline Skill

An OpenClaw skill that helps you contribute to open source repos. It triages issues, builds fixes, and submits PRs — with you in the loop via Telegram.

## How it works

PR Sentinel runs a 6-phase pipeline on every heartbeat:

| Phase | What |
|-------|------|
| **0** | Check existing PRs for maintainer feedback |
| **1** | Triage open issues, claim unclaimed ones |
| **2** | Plan the fix with OpenCode (read-only analysis) |
| **3** | Build the fix with OpenCode (file edits) |
| **4** | Run tests, fix regressions |
| **5** | Send technical brief to Telegram for approval |
| **6** | Push and submit PR as draft |

## Install

```bash
openclaw skills install git:chrislazar25/pr-sentinel-skill
```

## Setup

You need:

- [OpenClaw](https://github.com/openclaw/openclaw) + Telegram connected
- `gh` (GitHub CLI) authenticated
- `opencode` (`npm i -g opencode-ai`)
- A local clone of your target repo
- A Python virtualenv for test execution

Then set your config in the workspace `TOOLS.md`:

```
TARGET_REPO: "Owner/Repo"
FORK_USER: "your-github-username"
LOCAL_REPO_DIR: "/path/to/repo"
PYTHON_ENV_PATH: "/path/to/venv"
```

See [`references/BOOTSTRAP.md`](references/BOOTSTRAP.md) for full setup walkthrough.

## Architecture

```
Telegram (you)
    │
    ▼
OpenClaw Agent ──heartbeat──► Pipeline Loop
    │                              │
    │                              ├─ Phase 0-1: GitHub Issues/PRs
    │                              ├─ Phase 2-3: OpenCode plan+build
    │                              ├─ Phase 4: Test runner
    │                              ├─ Phase 5: Telegram approval gate
    │                              └─ Phase 6: PR submission
    │
    ▼
Your target repo PRs
```

## What's inside

```
pr-sentinel-skill/
├── SKILL.md           # Full pipeline definition (the skill)
├── AGENTS.md          # Workspace rules
├── SOUL.md            # Personality
├── IDENTITY.md        # Who this agent is
├── HEARTBEAT.md       # Heartbeat pipeline trigger
├── TOOLS.md           # Config template
├── KANBAN.md          # Sprint board
├── MEMORY.md          # Long-term memory (clean slate)
└── references/
    └── BOOTSTRAP.md   # Setup guide
```

## License

MIT