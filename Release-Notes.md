# Release notes

## V2 rebuild — 2026-09-12

Implemented:

- Amber responsive React dashboard and player charts.
- Versioned provider-independent schema and local Yahoo browser adapter.
- Dedicated PostgreSQL snapshot, forecast, session and import-event storage.
- Projection-aware parsing, injury/bye exclusions, flex optimization and game locks.
- Public allowlist and anonymized league summaries; Google owner authorization.
- RSS headline ingestion from ESPN and Yahoo Sports.
- Mac launch-agent installer with overlapping-run protection and adaptive import cadence.
- Docker deployment isolated from existing VPS services.

Verified: production TypeScript/build and 11 core tests pass. The GitHub Validate workflow passed for the fresh main commit. Unattended Yahoo import succeeded with 17 roster players, 1,195 distinct pool players and 59 total pages. The replacement is live on HTTPS through Caddy port 3102. End-to-end API checks passed for health, upload authorization, schema validation, duplicate idempotency, public privacy and OAuth state rejection. Google owner login succeeded and exposed private league sections only to the owner. Desktop/mobile layout, player search and historical charts were inspected. The macOS launch agent is installed with a 900-second check interval.

Limitations: historical charts require repeat imports; forecast accuracy requires completed outcomes after a stored pregame forecast. Player pool coverage follows imported pages. A current-week trade/waiver scenario and gated empirical forecast calibration are implemented. Independent multi-source projection blending, full historical Yahoo parity and autonomous team writes are not claimed by this release.

A private Git bundle preserves the original code before the authorized repository rebuild. Never commit that bundle or captured private league pages.

## Import verification follow-up — 2026-09-12

The full manual unattended import remains the last successful dataset (2026-09-13 00:06 UTC). Subsequent launch-agent runs reached player pagination but Yahoo returned HTTP 999 with an empty response. Scheduled refresh is installed but a complete scheduled refresh has not yet been verified. Added explicit access-block detection and a one-hour minimum cooldown rather than repeated requests. The portal continues to serve 17 roster players and 1,195 pool players from the last complete snapshot. TypeScript validation passes for this follow-up.

## On-demand imports and NFL depth roles — 2026-09-12

Deployed the owner refresh queue and minute-based Mac polling, plus a searchable/paginated full trade and waiver candidate list with NFL position and ESPN starter/backup depth role. Verified a real owner button click queues the request, worker-token retrieval sees it, and unauthenticated POST/GET are rejected. Verified the live Michael Penix Jr. row shows QB / Atlanta / Backup (2). All 12 tests and production build pass. The queued refresh respects the existing Yahoo HTTP 999 cooldown; a new full Yahoo import is not claimed.
