# FUTURE-WORK.md — Parked Ideas

Ideas that were discussed but deliberately not built yet. Nothing here is committed to — pick up when the core flow is validated.

## Screenshots / visual evidence in Phase 4

- Status: **parked**. Default evidence = repro log + env description + fix log.
- Path: opencode has no built-in screenshot tool, but MCP servers can do it:
  - **Playwright MCP** (`@playwright/mcp`) — `browser_screenshot`, works for UI/browser bugs
  - **Chrome DevTools MCP** — same class of capability
  - opencode renders images via its `read` tool, so a captured PNG could be attached to the verdict card as MEDIA
- Blocked on: deciding it's worth an MCP dependency + a browser runtime on the host. Most CLI/library bugs are better proven by logs anyway. Only revisit for UI-heavy repos.

## Repo-score refresh

- Status: **parked by design**. Scores in STATE.json are frozen between discovery runs (discovery only re-runs when the sweep is empty).
- Possible future: weekly background refresh of scores, or refresh when a repo's PR dies in the graveyard.

## Per-repo context folders for contributed repos

- Status: **parked**. Was originally discussed as a repo-specific context folder; user clarified that context belongs to the loopr skill itself (this folder). If multi-repo work grows complex, revisit per-issue evidence folders under `context/`.

## Proposal comment as draft PR body

- Status: **parked**. The proposal comment (root cause + approach) is 90% of the eventual PR body. Could auto-seed Phase 6 `--body` instead of writing it fresh.

## Comment gate variants

- Status: **parked**. Current gate: user validates the issue comment before posting. Possible variant: two-stage gate (also gate the PR-linked follow-up comment).

## KANBAN automation

- Status: **parked**. Triage/repo scores are logged to KANBAN.md manually per phase. Could add a tiny script that appends rows from STATE.json.