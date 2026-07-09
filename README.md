# 🛡️ Loopr

An OpenClaw skill that turns your AI agent into an autonomous PR contributor. It watches a GitHub repo, finds open issues, builds fixes using AI, and submits PRs — with you approving each one through Telegram.

---

## 📋 What You Need First

Before anything else, make sure you have these **four things**:

| # | What | Why |
|---|------|-----|
| 1 | A **GitHub account** | To fork repos and submit PRs |
| 2 | **Telegram** installed on your phone | To chat with the agent |
| 3 | A **computer** (any OS) | To run the agent locally |
| 4 | A **GitHub repo** you want to contribute to | The target |

Got all four? Good. Let's go.

---

## 🪜 Step-by-Step Setup

### Step 1: Install OpenClaw

OpenClaw is the platform that runs the agent. Pick your OS:

<details>
<summary><b>Windows</b></summary>

1. Download the installer from https://openclaw.ai/download
2. Run the installer (`.exe` or `.msi`)
3. Open "Command Prompt" or "PowerShell" from the Start menu
4. Type `openclaw version` and press Enter — you should see a version number

</details>

<details>
<summary><b>macOS</b></summary>

1. Open **Terminal** (search for it in Spotlight/Cmd+Space)
2. Copy and paste this command, then press Enter:
   ```bash
   curl -fsSL https://openclaw.ai/install.sh | sh
   ```
3. Wait for it to finish (30-60 seconds)
4. Type `openclaw version` and press Enter — you should see a version number

**Alternative (if you have Homebrew):**
```bash
brew install openclaw/tap/openclaw
```
</details>

<details>
<summary><b>Linux</b></summary>

1. Open **Terminal**
2. Copy and paste this command, then press Enter:
   ```bash
   curl -fsSL https://openclaw.ai/install.sh | sh
   ```
3. Wait for it to finish (30-60 seconds)
4. Type `openclaw version` and press Enter — you should see a version number
</details>

<details>
<summary><b>✅ Verify Step 1</b></summary>

Run this in your terminal:
```bash
openclaw version
```

Expected output: something like `v0.x.x` (a version number).

If you get an error like "command not found", OpenClaw didn't install correctly. Try restarting your terminal and running the install command again.
</details>

---

### Step 2: Connect Telegram to Your Agent

This is how you'll talk to your PR bot.

1. In your terminal, run:
   ```bash
   openclaw gateway start
   ```
2. Open your web browser and go to: **http://localhost:8080**
3. You should see the OpenClaw dashboard
4. Find the **Telegram** section and click **Connect**
5. Follow the on-screen instructions (you'll get a QR code or a link to open on your phone)
6. Once connected, send a test message to your bot on Telegram — it should reply

> ⚠️ **Can't find the Telegram section?** The exact steps depend on your OpenClaw version. See the [official Telegram guide](https://docs.openclaw.ai/telegram) for screenshots.

<details>
<summary><b>✅ Verify Step 2</b></summary>

Send a message like "hello" to your bot on Telegram. If it replies, you're good. If not, restart OpenClaw (`openclaw gateway restart`) and try again.
</details>

---

### Step 3: Install the Loopr Skill

This installs the PR pipeline into your agent.

In your terminal, run:
```bash
openclaw skills install git:chrislazar25/loopr-skill
```

Expected output:
```
Installing skill from git:chrislazar25/loopr-skill
✅ Installed loopr
```

<details>
<summary><b>Alternative: Manual install</b></summary>

If the command above doesn't work, do this instead:
```bash
git clone https://github.com/chrislazar25/loopr-skill.git
cp -r loopr-skill ~/.openclaw/workspace/skills/
```
</details>

<details>
<summary><b>✅ Verify Step 3</b></summary>

Run:
```bash
openclaw skills list
```

You should see `loopr` in the list.
</details>

---

### Step 4: Install GitHub CLI

This is how the agent will interact with GitHub.

**Windows:**
1. Download from https://cli.github.com
2. Run the installer
3. Open Command Prompt and run:
   ```bash
   gh auth login
   ```
4. Follow the prompts — select "GitHub.com", then "Login with a browser"
5. A code will appear. Press Enter to open your browser, enter the code, and authorize

**macOS:**
```bash
brew install gh
gh auth login
```
Follow the browser login prompts.

**Linux (Debian/Ubuntu):**
```bash
sudo apt install gh
gh auth login
```

**Linux (Fedora):**
```bash
sudo dnf install gh
gh auth login
```

<details>
<summary><b>✅ Verify Step 4</b></summary>

Run:
```bash
gh auth status
```

Expected output: `✓ Logged in to github.com account your-username`
</details>

---

### Step 5: Install OpenCode

OpenCode is the AI that writes the code fixes.

```bash
npm install -g opencode-ai
```

> ⚠️ **Don't have Node.js?** Install it from https://nodejs.org (download the "LTS" version), then run the command above.

**macOS alternative:**
```bash
brew install opencode
```

<details>
<summary><b>✅ Verify Step 5</b></summary>

Run:
```bash
opencode --version
```

Expected: a version number like `1.x.x`
</details>

---

### Step 6: Fork and Clone Your Target Repo

1. Go to a GitHub repo you want to contribute to (e.g. `OwnerName/RepoName`)
2. Click the **Fork** button (top-right corner of the page)
3. In your terminal:
   ```bash
   cd ~
   git clone https://github.com/YOUR_GITHUB_USERNAME/REPO_NAME.git
   cd REPO_NAME
   git remote add upstream https://github.com/ORIGINAL_OWNER/REPO_NAME.git
   ```

> Replace `YOUR_GITHUB_USERNAME`, `REPO_NAME`, and `ORIGINAL_OWNER` with the actual values.

<details>
<summary><b>✅ Verify Step 6</b></summary>

```bash
git remote -v
```

You should see two remotes: `origin` (your fork) and `upstream` (the original repo).
</details>

---

### Step 7: Set Up a Python Virtual Environment

The agent needs this to run tests.

```bash
# macOS / Linux
python3 -m venv ~/envs/REPO_NAME_env
source ~/envs/REPO_NAME_env/bin/activate
pip install -e ~/REPO_NAME
deactivate
```

**Windows (PowerShell):**
```powershell
python -m venv $env:USERPROFILE\envs\REPO_NAME_env
$env:USERPROFILE\envs\REPO_NAME_env\Scripts\Activate.ps1
pip install -e $env:USERPROFILE\REPO_NAME
deactivate
```

> Replace `REPO_NAME` with the name of your forked repo.

<details>
<summary><b>✅ Verify Step 7</b></summary>

```bash
source ~/envs/REPO_NAME_env/bin/activate
python --version
deactivate
```

Should show a Python version (3.8 or higher).
</details>

---

### Step 8: Configure TOOLS.md

This tells the agent where everything is.

Open the file `~/.openclaw/workspace/TOOLS.md` in any text editor. Replace the contents with this template:

```
- TARGET_REPO: "OWNER/REPO_NAME"
- FORK_USER: "YOUR_GITHUB_USERNAME"
- LOCAL_REPO_DIR: "/path/to/REPO_NAME"
- WORKSPACE_DIR: "/path/to/.openclaw/workspace"
- PYTHON_ENV_PATH: "/path/to/REPO_NAME_env"
- UPSTREAM_REMOTE: "upstream"
- ORIGIN_REMOTE: "origin"
```

> Replace each value with your actual paths. For example, if your repo is `Aider-AI/aider` and your username is `janedoe`:
> - `TARGET_REPO` → `Aider-AI/aider`
> - `FORK_USER` → `janedoe`
> - `LOCAL_REPO_DIR` → `/home/janedoe/aider` or `C:\Users\janedoe\aider`

---

### Step 9: Update HEARTBEAT.md

Open the file `~/.openclaw/workspace/HEARTBEAT.md` in a text editor.

Delete everything in it and paste this:

```
## Loopr Pipeline

When you receive a heartbeat, execute the Loopr pipeline:

1. Phase 0 — Check open PRs for maintainer feedback
2. Phase 1 — Triage new issues (if no active follow-ups)
3. Phase 2-6 — Continue the active pipeline

See the loopr skill for full instructions.
```

---

### Step 10: Restart and Go

```bash
openclaw gateway restart
```

Wait 30 seconds, then send a message to your agent on Telegram.

---

## 🎯 Usage

Once set up, Loopr runs autonomously on a schedule. You interact with it through Telegram:

| You say... | It does... |
|-----------|-----------|
| **/loop** | Start the pipeline immediately (don't wait for heartbeat) |
| *(nothing)* | Pipeline runs automatically on heartbeat (~every 30 min) |
| **approve** | Approves a PR brief → submits the PR |
| **reject** | Cancels the current PR attempt |
| **status** | Shows what the pipeline is doing |

---

## ⚠️ Troubleshooting

**"Command not found" for any tool**
Reopen your terminal and try again. Some installs need a fresh terminal.

**Telegram not responding**
Run `openclaw gateway restart` and try sending a message again.

**GitHub says "not authenticated"**
Run `gh auth login` again and follow the browser prompts.

**Nothing happens on heartbeat**
Check that HEARTBEAT.md has the Loopr section. The pipeline triggers on heartbeat polls — these happen periodically, not immediately. You can wait or check `openclaw status` to see the heartbeat state.

---

## What's inside

```
loopr-skill/
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