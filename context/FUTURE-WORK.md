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

## Discovery-pick visibility (mid-run transparency)

- Status: **flagged, not built**. User feedback (2026-08-15): the first real discovery run bundled Phase 0 + Discovery + Triage into one final Telegram message — the user wasn't "in the loop" on which repos got picked until that single summary landed, and unfamiliar repo names came as a surprise.
- Possible fix: send a short Telegram note the moment Phase 1a *persists* repos to STATE.json (before Phase 1b triage runs) — e.g. "Discovery picked 3: lm-evaluation-harness (9), pr-agent (7), serena (7) — triaging issues now." Separates "here's what I'm now evaluating" from "here's what I found," so a cold-start discovery run isn't silent until the very end.
- Trade-off: more messages = more noise on every heartbeat. Probably only worth firing on true cold-start discovery (repos was empty), not on routine sweeps.

## Discovery-skip logic: score-clears-6 vs. actual pick made

- Status: **gap identified, not fixed**. SKILL.md Phase 1 literally reads: "If any issue scores ≥6 → proceed to Proposal Gate. Do NOT run discovery. If nothing clears 6 in any known repo → ... fall through to Phase 1a."
- Observed 2026-08-15 PM sweep: `pr-agent #2577` (score 8) and `docling #2722` (score 8*) both cleared ≥6 but were blocked by pre-existing competing PRs. Per the literal rule, discovery correctly did *not* fall through (something did clear 6) — but functionally nothing was pickable that cycle either.
- Open question: should the fall-through-to-discovery trigger be "no issue scores ≥6" (current) or "no issue was actually picked" (accounts for collision-blocking)? The latter avoids the rotation going permanently stale if every high-scoring issue in it keeps colliding with other contributors — a real risk, since repo selection specifically favors high external-merge-rate repos, which is exactly what attracts other contributors racing for the same easy issues.
- Not changing this without discussing it — genuinely unclear which behavior is better, could thrash on discovery too eagerly if flipped.

## Repo "what it does" not captured at discovery

- Status: **gap identified, not fixed**. STATE.json / STATE.example.json schema has no `description` field — score + merge metrics only. User had to ask after the fact what lm-evaluation-harness / pr-agent / serena actually do.
- Possible fix: Phase 1a's first evidence call (`repos/{owner}/{repo}`) already returns GitHub's own `description` field for free — cheap to persist that string into STATE.json and the KANBAN.md Repo Scores table alongside score/metrics, so discovery summaries are self-explanatory without a follow-up question next time.

## No onboarding framing in outbound messages — jumps straight to technical

- Status: **flagged, not built**. User feedback (2026-08-15): "when it gets back a message, it just directly starts explaining stuff technically without any pre-text of the repo or anything like that. It should understand that [I] don't [know] and should like onboard or something along those lines."
- Concrete example: the first discovery-run summary opened with *"Discovery (repos was empty): swept {ai, agent, eval} × {py, js}, scored 6 candidates... • lm-evaluation-harness (9) — 95% external merge rate, 19 good-first-issues..."* — zero framing on what lm-evaluation-harness even is, or why "external merge rate" is the thing being optimized, before the numbers start. Same pattern in Phase 0/Triage summaries: jargon (`gfi`, `external merge rate`, `≥6 gate`) fired at the reader cold.
- This isn't just a Phase 5 problem. Phase 5 already has *stated* intent along these lines ("The reviewer may have zero prior knowledge of this repo... get them to a confident sign-off decision in the fewest words" — see SKILL.md Phase 5 header), but the same courtesy isn't extended to Phase 0 / Discovery / Triage messages, which just dive in.
- Possible fix: **first-mention rule** — the first time a repo (or a piece of pipeline jargon) appears in a Telegram message, lead with one plain-English orientation clause before the technical breakdown. e.g. *"lm-evaluation-harness — a benchmark-testing library used by HuggingFace's model leaderboard — scored 9: 95% of its outside contributions get merged."* rather than leading with the score.
- Dovetails with the "Repo what it does" item directly above — same underlying data (GitHub's `description` field), just consumed for message framing instead of only KANBAN logging. Doing that item well mostly solves this one for the repo-identity case; the jargon-gloss case (gfi, merge rate, the ≥6 gate itself) is a separate, smaller fix — a one-time explainer the first time each term is used in a thread, not every time.

## Whiteboard-style explanations: downstream flow snapshot + blast radius

- Status: **flagged, not built**. User feedback (2026-08-15): wants explanations delivered like someone "go[ing] to a whiteboard and explain[ing] stuff" — a narrated walkthrough, not a text dump — and specifically wants the walkthrough's scope to include (1) a **current downstream flow snapshot** (what the affected code path looks like today — who calls into it, what depends on it — before the change lands) and (2) **possible things that might break** as a consequence of this kind of change (blast radius).
- Reading: extends Phase 5's existing "diagram" step (see CONTEXT.md Architecture: verdict card → diagram → on-demand layers) rather than replacing it. Two asks bundled together: (a) the diagram should show the *current* state/flow around the touched code, not just a before/after diff of the patch, and (b) a companion risk list — other call sites, tests, config assumptions, or downstream consumers that could be affected — should ride alongside (or be an on-demand layer next to) the diagram.
- Dovetails directly with "No onboarding framing in outbound messages" above — same root instinct (reader has zero prior context) — but that item is about *plain-English glossing of jargon*; this one is about the *shape/depth of the diagram layer itself*.
- Possible fix: before drawing the Phase 5 diagram, run a quick call-site scan (grep for the changed function/class/symbol names across the repo) to (1) render the actual current downstream flow instead of a generic guess, and (2) derive a short "what could break" list from real call sites + whatever tests currently cover them, rather than a hand-wavy risk paragraph.
- Open question: default part of every Phase 5 brief, or on-demand only (reviewer asks for it)? Default risks bloating every brief against Phase 5's stated "fewest words" goal; on-demand keeps the compact default but requires the reviewer to know to ask for it.