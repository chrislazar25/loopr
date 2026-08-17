# TOOLS.md — Loopr Config

**⚠️ EDIT YOUR WORKSPACE TOOLS.md (not this template) with your actual paths before running the pipeline. This file ships template placeholders only.**

## Pipeline Variables

```markdown
- FORK_USER: "your-github-username"
- REPOS_DIR: "/home/you/Desktop/repos"          # base dir where forks get cloned
- WORKSPACE_DIR: "/home/you/.openclaw/workspace"
- PYTHON_ENVS_DIR: "/home/you/envs"             # one venv per repo, created on demand
- REPOS_FILE: "$WORKSPACE_DIR/STATE.json"       # live state at workspace root — never inside the skill folder
- UPSTREAM_REMOTE: "upstream"
- ORIGIN_REMOTE: "origin"
```

Target repos are no longer fixed: they are discovered dynamically (see SKILL.md Phase 1a) and persisted in STATE.json.

## OpenCode

- **Binary:** `$(which opencode)`
- **Config dir:** `~/.local/share/opencode/`

### Model Configuration (single source of truth)

Set these in your **workspace** `TOOLS.md` (not this template):

```markdown
# OpenCode Models
- OPENCODE_PLAN_MODEL: "anthropic/claude-3-5-haiku"      # Phase 2: planning (cheaper OK)
- OPENCODE_BUILD_MODEL: "anthropic/claude-fable-5"       # Phase 3: code edits (quality critical)
- OPENCODE_DEFAULT_MODEL: "anthropic/claude-fable-5"     # Fallback if plan/build not set
```

**Note:** OpenClaw's own model (triage, briefs, Telegram) is configured in your OpenClaw gateway config, not here.

## Google Custom Search (Github search not used for discovery)

Not applicable — discovery uses the GitHub Search API only (see KEYWORDS.md + SKILL.md Phase 1a for quotas and batching).

### Pipeline commands

Plan mode:
```bash
opencode run --agent plan --model ${OPENCODE_PLAN_MODEL} --dir $REPOS_DIR/<repo> "Analyze issue #<number> in <owner/repo>. Output strict step-by-step plan." > $WORKSPACE_DIR/PLAN-<number>.md
```

Build mode:
```bash
opencode run --agent build --model ${OPENCODE_BUILD_MODEL} --dir $REPOS_DIR/<repo> "Modify files executing: $(cat $WORKSPACE_DIR/PLAN-<number>.md)."
```

## Telegram Setup

- Channel: Telegram (configurable in OpenClaw gateway)
- Approval gates expect "approve" / "reject" / edits in chat