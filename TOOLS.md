# TOOLS.md — Loopr Config

**⚠️ EDIT THIS FILE with your actual paths before running the pipeline.**

## Pipeline Variables

```markdown
- TARGET_REPO: "Owner/Repo"  # e.g. Aider-AI/aider
- FORK_USER: "your-github-username"
- LOCAL_REPO_DIR: "/home/you/path/to/repo"
- WORKSPACE_DIR: "/home/you/.openclaw/workspace"
- PYTHON_ENV_PATH: "/home/you/envs/repo_env"
- UPSTREAM_REMOTE: "upstream"
- ORIGIN_REMOTE: "origin"
```

## OpenCode

- **Binary:** `$(which opencode)`
- **Default model:** anthropic/claude-fable-5 (requires `ANTHROPIC_API_KEY` in opencode auth)
- **Config dir:** `~/.local/share/opencode/`
- **Note:** plan and build commands pass `--model anthropic/claude-fable-5` explicitly. To downgrade a phase later (e.g. cheaper build model), change the flag on that command only. The OpenClaw agent itself (triage, briefs, Telegram) uses the model set in your OpenClaw gateway config, not this file.

### Pipeline commands

Plan mode:
```bash
opencode run --agent plan --dir $LOCAL_REPO_DIR "Analyze issue #<number>. Output strict step-by-step plan." > $WORKSPACE_DIR/PLAN-<number>.md
```

Build mode:
```bash
opencode run --agent build --dir $LOCAL_REPO_DIR "Modify files executing: $(cat $WORKSPACE_DIR/PLAN-<number>.md)."
```

## Telegram Setup

- Channel: Telegram (configurable in OpenClaw gateway)
- Approval gate expects "approve" or "reject" in chat