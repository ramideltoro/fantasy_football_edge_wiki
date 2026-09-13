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
