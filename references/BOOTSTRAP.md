# PR Sentinel — First Run Setup

## Prerequisites

1. **OpenClaw** installed — https://github.com/openclaw/openclaw
2. **GitHub CLI** — `gh auth login`
3. **OpenCode** — `npm i -g opencode-ai`
4. **A local clone** of the repo you want to contribute to
5. **A Python virtualenv** for running tests
6. **Telegram connected** to your OpenClaw instance

## Install the Skill

```bash
openclaw skills install git:chrislazar25/pr-sentinel-skill
```

Or clone + copy manually:

```bash
git clone https://github.com/chrislazar25/pr-sentinel-skill
cp -r pr-sentinel-skill ~/.openclaw/workspace/skills/
```

## Configure

1. Open your workspace `TOOLS.md`
2. Set `TARGET_REPO`, `FORK_USER`, `LOCAL_REPO_DIR`, `WORKSPACE_DIR`, `PYTHON_ENV_PATH`, `UPSTREAM_REMOTE`, `ORIGIN_REMOTE`
3. Set `HEARTBEAT.md` to include the pipeline trigger (see skill's HEARTBEAT.md)

## First Heartbeat

Restart OpenClaw (`openclaw gateway restart`) and wait for the next heartbeat, or trigger one manually. The pipeline will start at Phase 0.

## Usage

- **Chat with the agent** like you would any Telegram contact
- Type **"approve"** to advance past the human gate in Phase 5
- Type **"reject"** to kill a PR attempt and clean up
- Type **"status"** to get pipeline state + KANBAN summary