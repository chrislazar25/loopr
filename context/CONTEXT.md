# CONTEXT.md — Loopr's Own Context

This file carries the skill's evolving state: what it is, why it's shaped this way, and open decisions. It is **not** about target repos — those live in STATE.json + KANBAN.md.

## What Loopr Is

Autonomous open-source PR pipeline: discovers candidate repos, triages issues, proposes fixes on the issue before building, builds+plans via OpenCode, gates on a human, files PRs, and maintains them. Driven by heartbeats or `/loop` on Telegram.

## Architecture

- **Phase 0** — PR maintenance: iterate owned PRs from STATE.json, react to maintainer feedback.
- **Phase 1** — Known-repo sweep: fresh issues in repos already in rotation (cheap, 1-2 API calls each). Proceeds to discovery only if the sweep is empty.
- **Phase 1a** — Repo discovery: GitHub Search API matrix from KEYWORDS.md (keyword × language), batched with backoff under the search quota (10 req/min, 1000 results/query cap). ~5 evidence calls per candidate (repo metadata + merged-PR author_association breakdown + GFI comment counts). Persist top 2-3 to STATE.json.
- **Phase 1b** — Issue triage in newly discovered repos (issue-tier gate only; repo tier is already satisfied at discovery).
- **Proposal gate** — Draft an issue comment (root cause hypothesis + approach + files touched). **Human gate**: user validates/edits before posting. Post, then build immediately — never wait for a reply.
- **Phase 2-4** — Plan/Build/Test via OpenCode (mandatory, zero exceptions). Evidence pack = bug repro log + env description + fix log (no screenshots; see FUTURE-WORK.md).
- **Phase 5** — Layered comprehension brief on Telegram (verdict card → diagram → on-demand layers).
- **Phase 6** — PR from fork. Link the PR in a comment on the issue. Feedback: "go ahead" → nothing; modifications → amend the **same branch**, PR updates in place (never close/reopen); negative → close PR + delete branch.

## Key Decisions

- **Two-tier merge-likelihood gate**: repo receptiveness is proven once at discovery and frozen in STATE.json; the issue tier scores only issue-local signals. Never re-score repo attributes per issue.
- **Proposal before build, build before approval**: the issue comment is informational/derisking; outward momentum is kept compact.
- **State over static config**: TARGET_REPO no longer exists — repos rotate via STATE.json.
- **Clone root**: `REPOS_DIR` (user's Desktop/repos) — forks clone to `$REPOS_DIR/<repo>/` at first contact, reused after.
- **Model**: fable (anthropic/claude-fable-5) for both OpenClaw (opencode/claude-fable-5 via opencode provider) and OpenCode `--model` flags. DeepSeek flash stays registered as fallback.

## Links

- STATE.json schema: see STATE.example.json
- Future work: FUTURE-WORK.md (same folder)