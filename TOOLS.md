# TOOLS.md — Loopr Config

**⚠️ EDIT YOUR WORKSPACE TOOLS.md (not this template) with your actual paths before running the pipeline. This file ships template placeholders only.**

## Pipeline Variables

```markdown
- FORK_USER: "your-github-username"
- REPOS_DIR: "/home/you/Desktop/repos"          # base dir where forks get cloned
- WORKSPACE_DIR: "/home/you/.openclaw/workspace"
- PYTHON_ENVS_DIR: "/home/you/envs"             # one venv per repo, created on demand
- REPOS_FILE: "$WORKSPACE_DIR/loopr/STATE.json" # live state — gitignored, never commit
- UPSTREAM_REMOTE: "upstream"
- ORIGIN_REMOTE: "origin"
```

Target repos are no longer fixed: they are discovered dynamically (see SKILL.md Phase 1a) and persisted in STATE.json.

## OpenCode

- **Binary:** `$(which opencode)`
- **Default model:** anthropic/claude-fable-5 (requires `ANTHROPIC_API_KEY` in opencode auth)
- **Config dir:** `~/.local/share/opencode/`
- **Note:** plan and build commands pass `--model anthropic/claude-fable-5` explicitly. To downgrade a phase later (e.g. cheaper build model), change the flag on that command only. The OpenClaw agent itself (triage, briefs, Telegram) uses the model set in your OpenClaw gateway config, not this file.

## Google Custom Search (Github search not used for discovery)

Not applicable — discovery uses the GitHub Search API only (see KEYWORDS.md + SKILL.md Phase 1a for quotas and batching).

### Pipeline commands

Plan mode:
```bash
opencode run --agent plan --model anthropic/claude-fable-5 --dir $REPOS_DIR/<repo> "Analyze issue #<number> in <owner/repo>. Output strict step-by-step plan." > $WORKSPACE_DIR/PLAN-<number>.md
```

Build mode:
```bash
opencode run --agent build --model anthropic/claude-fable-5 --dir $REPOS_DIR/<repo> "Modify files executing: $(cat $WORKSPACE_DIR/PLAN-<number>.md)."
```

## Telegram Setup

- Channel: Telegram (configurable in OpenClaw gateway)
- Approval gates expect "approve" / "reject" / edits in chat