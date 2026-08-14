# KEYWORDS.md — Repo Discovery Targets

Editable without touching pipeline logic. Phase 1a builds the search matrix as `keyword × language` and runs every combination.

## Fields

```
ai:      repos working on AI/LLM tooling, agents, inference
agent:   autonomous/agent frameworks, tool-use, multi-agent
eval:    evaluation harnesses, benchmarks, evals for LLMs
```

Edit buckets freely — add or remove keywords. Each keyword generates one query per language, so keep the matrix small enough to fit the search quota budget (10 req/min, batched with backoff).

## Languages (for now)

```markdown
- python
- javascript
```

## Quality filters (applied to every candidate repo)

```markdown
- min_good_first_issues: 5
- min_stars: 100
- max_pushed_age_days: 180     # repo must be active recently (avoid stale backlogs)
- archived: false
- max_merge_days: 30           # median days-to-merge any higher = skip
- min_external_merge_rate: 0.2 # fraction of recently merged PRs from external authors
- max_open_pr_age_days: 60     # graveyard check: most open PRs older than this = skip
```

## Scoring (0-10 per repo, = "trending ∧ active ∧ external-PR-friendly")

- **+3** pushed_at within 45 days and stars trending up
- **+2** external merge rate ≥ min_external_merge_rate in last 90 days
- **+2** median days-to-merge ≤ max_merge_days
- **+2** ≥ 10 open good-first-issues with low comment counts (uncontested picks)
- **+1** recent merged PRs show quick, low-churn review (few review rounds)

**Persist top 2-3 repos to STATE.json after each discovery run. Log scores to KANBAN under `## Repo Scores`.**