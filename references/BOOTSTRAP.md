# Loopr — First Run Setup

Follow these steps in order. If something doesn't work, there's a ✅ Verify check at the end of each step you can use to find where the issue is.

## Step 1: Install OpenClaw

See README.md for OS-specific commands.

✅ **Verify:** `openclaw version` shows a version number

## Step 2: Connect Telegram

OpenClaw dashboard → Telegram section → Connect → Scan QR or follow link on phone

✅ **Verify:** Send "hello" to your bot on Telegram. It replies.

## Step 3: Install the Skill

```bash
openclaw skills install git:chrislazar25/loopr-skill
```

✅ **Verify:** `openclaw skills list` shows `loopr`

## Step 4: Install GitHub CLI + Auth

Install from https://cli.github.com, then:

```bash
gh auth login
```

✅ **Verify:** `gh auth status` says logged in

## Step 5: Install OpenCode

```bash
npm install -g opencode-ai
```

✅ **Verify:** `opencode --version` shows a version

## Step 6: Fork + Clone Target Repo

Fork on GitHub, then:

```bash
cd ~
git clone https://github.com/YOUR_USERNAME/REPO_NAME.git
cd REPO_NAME
git remote add upstream https://github.com/ORIGINAL_OWNER/REPO_NAME.git
```

✅ **Verify:** `git remote -v` shows `origin` and `upstream`

## Step 7: Python Virtualenv

```bash
python3 -m venv ~/envs/repo_env
source ~/envs/repo_env/bin/activate
pip install -e ~/REPO_NAME
deactivate
```

✅ **Verify:** `source ~/envs/repo_env/bin/activate && python --version` works

## Step 8: Configure TOOLS.md

Edit `~/.openclaw/workspace/TOOLS.md` and paste this template:

```
- TARGET_REPO: "OWNER/REPO_NAME"
- FORK_USER: "YOUR_GITHUB_USERNAME"
- LOCAL_REPO_DIR: "/path/to/REPO_NAME"
- WORKSPACE_DIR: "/path/to/.openclaw/workspace"
- PYTHON_ENV_PATH: "/path/to/REPO_NAME_env"
- UPSTREAM_REMOTE: "upstream"
- ORIGIN_REMOTE: "origin"
```

Replace each `OWNER`, `REPO_NAME`, `YOUR_GITHUB_USERNAME`, and `/path/to/...` with the actual values for your setup.

## Step 9: Update HEARTBEAT.md

Put this in `~/.openclaw/workspace/HEARTBEAT.md`:

```
## Loopr Pipeline

When you receive a heartbeat, execute the Loopr pipeline:
1. Phase 0 — Check open PRs for maintainer feedback
2. Phase 1 — Triage new issues (if no active follow-ups)
3. Phase 2-6 — Continue the active pipeline
```

## Step 10: Go

```bash
openclaw gateway restart
```

Wait 30 seconds. Message your bot on Telegram.