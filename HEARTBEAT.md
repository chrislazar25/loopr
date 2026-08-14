## Loopr Pipeline

When you receive a heartbeat, execute the Loopr pipeline:

1. **Phase 0** — Check open PRs for maintainer feedback
2. **Phase 1** — Triage new issues (if no active follow-ups)
3. **Phase 2-6** — Continue the active pipeline

See `{baseDir}/SKILL.md` for full phase details and the `loopr` skill.
## Wake Recovery

If the timestamp gap since the last heartbeat is much larger than the heartbeat interval, the host likely slept. On the first heartbeat after a gap:

1. Send Telegram: "Back online after ~<gap> asleep. Resuming."
2. Do NOT start a new pipeline. First re-read KANBAN.md and check for an in-flight phase; resume it from its last completed step.
3. Re-run Phase 0 (PR feedback check) before anything else — maintainer activity may have arrived during the gap.