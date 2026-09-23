# Release notes

## 2026-09-19 — The coach takes over the clubhouse

Added a narrative-matched reaction GIF to the locker-room read, with seven game states, six attributed clips, pause/resume, remembered preference, reduced-motion stills and a resilient media fallback. Updated editorial copy throughout the existing sections and Qwen’s future commentary instructions to the same funny, dramatic, profane coach’s voice. Team briefing templates update immediately; cached model prose follows the normal refresh cycle. Calculations, sources, navigation and the grouped Overview remain intact.

Deployed as application commit `7465011`. All 127 tests passed locally and against the built container. Production health, CSP, live reaction selection, desktop/mobile layout, all mobile routes, GIF pause/resume, reduced motion and media-failure behavior passed verification. See [Locker-room read](Locker-Room-Read.md) for the reaction rules, voice contract and media behavior. Rollback backup: `/var/backups/fantasy-football-edge/coach-personality-20260919/` (source, database and prior image reference).

## 2026-09-19 — The coach has the film

Replaced the basic Overview commentary with a styled, profane and optimistic coaching assessment grounded in live matchup projections, scored points, original-to-live movement, completed opponent games and legal lineup options. The expandable scouting report covers both teams’ playmakers, recent form, availability and sortable game receipts. Fresh data updates the read through existing dashboard polling; phrasing rotates every 30 minutes. Missing history, thin samples and stale sources remain explicit. Final results switch to a postgame read.

Production build and all 123 tests pass. See [Locker-room read](Locker-Room-Read.md) for sources, calculations, refresh semantics and validation.

## 2026-09-19 — A focused overview and grouped navigation

Overview now contains the health board, the weekly team-versus-opponent scorecard and commentary. My Team groups current lineup work and forward planning; Waivers groups player discovery and pickup tools; Research groups news/analysis, past results and preseason drafting. The scoring charts moved into a closed Roster disclosure, the shortlist moved to Waivers and full refresh controls moved to Operations. All existing bookmark URLs remain valid.

The production build and 113 tests pass. Desktop and 390 px browser checks verified the regrouped navigation, moved disclosures, health popup, actual chart labels and four lineup projection choices. See [Navigation and Overview](Navigation-and-Overview.md) for the current tool map and release boundaries.

## 2026-09-18 — Eight connected game-plan tools

Added kickoff availability alerts and opt-in open-page browser notifications; a persistent pregame forecast report card; three-week roster planning; material changes since the last visit; protect/chase risk preferences; multi-week waiver impact; FLEX timing and late-injury contingencies; and weekly decision/outcome recaps. Overview links directly to relevant actions. Detailed tools are grouped under My Team, Waivers and Research, with shareable tab URLs, player popups and sortable tables.

Validated with 94 passing tests and a production TypeScript/Vite build. Live checks covered the risk tradeoff, sportsbook risk exclusion, sortable planning table, immediate add/drop comparison, depth changes, three-week totals, matched-game report filter, final/in-progress recaps, kickoff source health, privacy and 390 px mobile layout. The first successful game-plan run stored 1,575 pregame forecasts and graded 16 original Yahoo observations; Qwen and combined remain prospective. All 32 schedule teams and fresh ESPN checks for the roster’s 13 NFL teams were present. Existing six-hour sportsbook scheduling remained healthy.

See [Game plan and receipts](Game-Plan-and-Receipts.md) for methods, source freshness, notification limits, historical-data limitations and rollback.

## 2026-09-18 — League-scored Qwen and the weekly overview

Replaced unbounded Qwen point generation with league-scored historical candidates and model-selected adjustments, fixing the Jason Sanders 44.50 outlier (verified live at 10.24). Added clickable point/probability explanations, K/DEF modeled sportsbook estimates with all-book receipts, and the requested waiver column change. Moved the health board to Overview and next-move panel to Research; added a weekly matchup commentary, actual chart labels, matching legend colors, the two team-name aliases and Apple system typography. The six-hour odds refresh remains active. A dedicated local inference runtime now prevents other applications' context sizes from stalling fantasy requests. See [League scoring and explanations](League-Scoring-and-Explanations.md) for method details, 68-test validation and rollback, and [Operations](Operations.md) for runtime configuration.

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

## 2026-09-18 — Four playbooks and sorting everywhere

- Waiver start percentages and Qwen numbers open their explanation directly, without the repeated “Why this number?” caption.
- Every table has keyboard-accessible ascending/descending column sorting. Player tables sort the entire filtered set before pagination and preserve checked players.
- My Team's lineup recommendations have Yahoo-only, Qwen-only, bookies-only and combined toggles, with per-player inputs and explicit source coverage. The combined method completes partial sportsbook markets using uncovered league-scored historical components before averaging; bookies-only keeps partial-total labels.
- Tied lineups prefer fewer moves and do not recommend equivalent zero-gain FLEX shuffles.
- 77 tests and the production build pass. Desktop/mobile browser checks covered the four modes, pagination, percentage popup and sorting. See [the source and calculation contract](Lineup-Modes-and-Table-Sorting.md).

## September 19, 2026 — Scouting and league lab

Deployed workload/red-zone radar, three-week matchup/weather context, manual multi-player trades plus bounded automatic partner suggestions, rolling-priority claim guidance, league power totals, experimental playoff scenarios, an ADP practice board and source-specific lineup pins/exclusions. Overview provides direct entry points; the tools reuse sortable tables and player dossiers. See [Scouting and league lab](Scouting-and-League-Lab.md) for the complete source/calculation contract and limitations.

The read-only scouting import successfully captured all 12 rosters and schedules. Live `/api/edge-lab` returned 323 players, eight proposed one-for-one deals, 263 players with covered red-zone observations, 1,000 simulations with results for all 12 teams, and zero failed sources. The median-result rule and six-team Week-15 playoff setting were verified from Yahoo. Twenty-eight unfilled future starting slots were explicitly represented in the no-pickup scenario. A rolling historical baseline evaluation covered 4,990 held-out games. These tests do not establish calibrated playoff odds.

Validation: 113 automated tests passed locally and in the production Node 24 image; TypeScript/Vite production build succeeded. Browser checks covered table sorting, player/compare modals, trade selection, claim selection, lineup pins, draft undo and 390px mobile containment. Health and public scouting APIs returned HTTP 200. Public scouting responses exclude raw Yahoo sections and manager/authentication fields.

The positional matchup cache reduced a local full-dataset calculation from approximately 28 seconds to 11 seconds without changing the method. Source/database/image backups and container build/test logs are under `/var/backups/fantasy-football-edge/scouting-20260919/`. The existing Yahoo authorization migration and VegasInsider six-hour collector remain in place. No paid source subscription was added.

## September 23, 2026 — independent refresh and DEF opponents

- Refresh public research and queue forecasts every two hours using the last available roster, independently of Yahoo imports; retry failed research with backoff and preserve last-good data.
- Recover dashboard polling after stalled network requests.
- Show DEF's next opponent and fantasy points/game allowed to opposing defenses, using league scoring and labeled season/game samples.
- Validated with production build and 129 passing tests. See [Operations](Operations.md) for refresh behavior, dependencies and rollback.

## September 23, 2026 — six-position waiver shortlist

Each position now gets up to three eligible picks. DEF rankings use an explicit 50/50 blend of current-season defensive production and the next opponent's fantasy points allowed to defenses, with per-game evidence and sample-size warnings. Other positions use fresh Qwen or labeled Yahoo point forecasts. Historical Qwen priority scores cannot override the current shortlist. Missing DEF evidence is labeled and falls back without inventing a number. Build and 133 tests pass.
