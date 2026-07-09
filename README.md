# 🛡️ PR Sentinel

An OpenClaw skill that handles the full PR contribution pipeline — triages issues, builds fixes via OpenCode, and submits PRs. You stay in the loop via Telegram.

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

### 1. Install OpenClaw

**Linux / Windows (WSL):**
```bash
curl -fsSL https://openclaw.ai/install.sh | sh
```

**macOS:**
```bash
curl -fsSL https://openclaw.ai/install.sh | sh
# Or via Homebrew:
brew install openclaw/tap/openclaw
```

**Windows (native):**
Download the installer from [openclaw.ai](https://openclaw.ai) or use the [OpenClaw.app](https://openclaw.ai/download) bundle.

### 2. Install the Skill

Once OpenClaw is running:

```bash
openclaw skills install git:chrislazar25/pr-sentinel-skill
```

Or manually copy:

```bash
git clone https://github.com/chrislazar25/pr-sentinel-skill.git
cp -r pr-sentinel-skill ~/.openclaw/workspace/skills/
```

### 3. Install Dependencies

- **GitHub CLI** (`gh`) — [install guide](https://cli.github.com)
  - Then run: `gh auth login`
- **OpenCode** — `npm install -g opencode-ai`
  - Or: `brew install opencode` (macOS)
- **Python** (for test runner) — Python 3.8+

### 4. Configure

Set your config in the workspace `TOOLS.md`:

```
TARGET_REPO: "Owner/Repo"      # e.g. Aider-AI/aider
FORK_USER: "your-github-handle"
LOCAL_REPO_DIR: "/path/to/repo"    # local clone
WORKSPACE_DIR: "/home/you/.openclaw/workspace"
PYTHON_ENV_PATH: "/path/to/venv"   # virtualenv for tests
UPSTREAM_REMOTE: "upstream"
ORIGIN_REMOTE: "origin"
```

### 5. Connect Telegram

Set up Telegram in your OpenClaw gateway config. This is where you'll receive PR briefs and send approval/rejection commands.

### 6. Start the Loop

That's it. The pipeline triggers on each heartbeat. You'll get a Telegram message when Phase 5 hits — just reply "approve" to submit the PR.

## Architecture

```
Telegram (you)
    │
    ▼
OpenClaw Agent ──heartbeat──► Pipeline Loop
    │                              │
    │                              ├─ Phases 0-1: GitHub Issues/PRs
    │                              ├─ Phases 2-3: OpenCode plan+build
    │                              ├─ Phase 4: Test runner
    │                              ├─ Phase 5: Telegram approval gate
    │                              └─ Phase 6: PR submission
    │
    ▼
Your target repo PRs
```

## Commands

In Telegram chat with the agent:

- **approve** — approve PR and submit
- **reject** — cancel current PR attempt
- **status** — show pipeline state + KANBAN board

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