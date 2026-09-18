# Release notes

## 2026-09-18 — League-scored Qwen and the weekly overview

Replaced unbounded Qwen point generation with league-scored historical candidates and model-selected adjustments, fixing the Jason Sanders 44.50 outlier (verified live at 10.24). Added clickable point/probability explanations, K/DEF modeled sportsbook estimates with all-book receipts, and the requested waiver column change. Moved the health board to Overview and next-move panel to Research; added a weekly matchup commentary, actual chart labels, matching legend colors, the two team-name aliases and Apple system typography. The six-hour odds refresh remains active. See [League scoring and explanations](League-Scoring-and-Explanations.md) for method details, 67-test validation and rollback.

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

Added a dedicated **Waiver list** navigation section with NFL starter/backup as the first column, searchable available-player details, availability filtering, pagination and player-detail links. Production build validated.

Added the NFL starter/backup column to My roster using ESPN depth charts. Production build passed.

Added the Yahoo refresh operations section, live five-second log polling, worker heartbeat/cooldown state, queued-request status and page-by-page importer progress. Production build validated.

## Refresh freshness correction

Investigated the reported stale roster: the served snapshot was still captured at 2026-09-13 00:06 UTC, with subsequent Yahoo requests blocked by HTTP 999. Tightened successful-refresh semantics: Yahoo uploads must include completed coverage for roster, league, matchups, settings, transactions, schedule, draft, research, and O/K/DEF pool groups. Empty player pages fail instead of silently ending pagination. Duplicate uploads cannot fulfill pending requests. Capture timestamps now use import start, so a request arriving mid-import remains pending for a subsequent run. All views show the same snapshot timestamp and owner cooldown/pending status; dashboard polling is ten seconds. Thirteen tests pass. A new complete Yahoo import remains pending until Yahoo allows access.

Added one-ZIP-package data upload per completed refresh. A saved snapshot compressed from 219,754 to 32,116 bytes (about 85% smaller). All 15 tests and production build pass, including ZIP round-trip and malformed/oversized archive rejection. This reduces upload payload, not Yahoo browsing traffic. Worker remains paused.

Verified the reduced import end-to-end at 2026-09-13 01:48 UTC: one ZIP upload succeeded with 17 roster players, 50 available RB/WR/TE players from exactly two player pages, and all eight roster/league page groups (10 pages total). Production coverage confirms `players-WRT-top2` complete. The run took approximately 65 seconds from capture start. All 15 tests pass; the worker is enabled.

## Qwen player intelligence

Implemented post-import research/analysis jobs, cached nflverse weekly stats and snap counts, schedule/opponent enrichment, league-scored history, a transparent gated Yahoo/history forecast blend, model-based lineup optimization, Qwen evidence selection, player usage charts, source-health metadata, and prospective model-versus-Yahoo accuracy tracking. The first live research run fetched all five data files and analyzed 67 players. Twenty tests pass, including no-future-data, custom scoring, ZIP validation and constrained Qwen output. Existing Yahoo browsing remains limited to two W/R/T pages; the independent AI worker never browses Yahoo.

The numerical model is an experimental heuristic, not a trained or proven improvement. Week 1 forecasts remain Yahoo baselines until enough current-season games exist. Actual accuracy requires later completed-game imports. Injury evidence currently comes from Yahoo flags and matching RSS headlines, not a dedicated medical/injury feed. Qwen selects evidence and actions; it does not invent numeric scores or perform transactions.

Live verification completed on 2026-09-13 at 02:14 UTC: the local Qwen2.5 3B worker returned eight validated evidence selections, and the public AI insights page displayed the completed brief, 67-player analysis, historical usage charts, suggested lineup, source timestamps and zero-sample accuracy state. Worker routes rejected unauthenticated access (401); owner retry rejected anonymous requests (403). The Mac AI launch agent points to `importer/ai.ts`, independently of the Yahoo browser worker.

## September 18, 2026 — Game day, with receipts

Rebuilt roster and waivers around one scouting board and shared photo-rich player dossiers; added modal comparisons and instant outgoing/incoming scenarios; capped the waiver shortlist at three QBs; collapsed candidate and refresh detail; replaced Player Research with a health overview; exposed opponent team names while retaining manager privacy. Added actual NFL season starts from ESPN game rosters, independent Qwen-generated points and availability estimates, and a bolder amber/charcoal theme with original football copy and reduced-motion support. Sportsbook consensus was deferred by the owner. See [Game-day player experience](Game-Day-Experience.md) for sources, calculation definitions, validation and rollback.

## 2026-09-18 — VegasInsider sportsbook receipts

Added the Bookies projected column to roster and waivers, with the same data in player/compare dossiers. Numbers open a dedicated calculation popup. The source supplies a partial points subtotal, clearly labelled with missing components and exact raw quotes. All eight available sportsbooks are collected; two pick’em operators are shown separately. Added a persistent six-hour backend refresh, retry/backoff, stale/locked-data handling and source health endpoints. See [Sportsbook projections](Sportsbook-Projections.md) for the calculations and source limitations. This supersedes the earlier sportsbook deferral.
