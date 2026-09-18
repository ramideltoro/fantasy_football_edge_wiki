# Game-day player experience

Released September 18, 2026 at https://fantasy.ramideltoro.com.

## The scouting board

My Team → Roster and Waivers share the same player table. Its columns are Compare, NFL Starter / Backup, Fantasy position (the fantasy roster assignment, such as BN or W/R/T), Player, Slot (NFL position), Yahoo projected, Qwen projected, Bookies projected, Rostered, and Started. Waivers replaces Fantasy position with clickable **Qwen probability start**. Qwen point values also open a scoring-explanation popup. The roster search and separate Player Research tab have been removed. Position filters remain. On phones each row becomes a labeled player card.

Check at least two players to reveal **Compare players**. The dialog can compare every selected player. Clicking a player name opens the same dossier, including an ESPN photo or team logo, position, NFL role, availability, projections, source links, historical results, and imported stats. Missing source photos use a neutral icon.

Lineup & decisions caps its ten-player waiver shortlist at three quarterbacks, followed by the highest projected non-QBs. The trade/waiver scenario has two dropdowns: choose an outgoing roster player, then an incoming candidate. The complete comparison opens immediately. The candidate browser is collapsed by default. These controls are advisory and never submit a Yahoo transaction.

## Projection sources

**Yahoo projected** always preserves the Yahoo number, including a missing value. It is not replaced by another model.

**Qwen projected** is returned by the existing local `qwen2.5:3b` model. The active method is `qwen-points-v5`: six-player batches, exact player-ID keys, league-scored historical baselines, position/depth evidence, availability, reporting and available ESPN game-line context. Qwen selects a bounded numerical adjustment from scored candidates; Yahoo projections are excluded. This corrects the earlier v4 kicker/game-total confusion. See [League scoring and explanations](League-Scoring-and-Explanations.md) for the full method, scoring receipts and limitations.

The model supplies points, whole-number subjective play/start percentages, and evidence keys. Ranges were removed to keep the small-model calculation focused. Team-defense units have no individual play/start probability. The server builds the displayed evidence summary from those keys; unverified free-form model stories are not published. Validation rejects missing/cross-player IDs, fractional or out-of-bounds percentages, contradictory availability, positive forecasts with zero chance to play, estimates made after kickoff, and unsupported point estimates. Null means insufficient evidence or too late for a pre-game forecast. A model number is never invented just to fill a cell.

Forecasts older than four hours are labeled stale and do not drive the lineup optimizer; same-week prior results remain available in the dossier for up to 24 hours. Injury mismatches invalidate a stored forecast. New jobs are prioritized for the owner's roster; news/analysis work is interleaved to avoid starving reporting. A rejected projection gets one immediate Qwen correction pass with the validator’s feedback; no rejected response is published. Failed jobs retry at most three times with ten-minute leases/backoff. IDs, sources, completion time and model method remain inspectable. This is an experimental model, not a calibrated probability system or established improvement over Yahoo.

**Bookies projected** was subsequently authorized and added using every available sportsbook on VegasInsider's public NFL boards, refreshed on the backend every six hours. Offensive numbers are explicitly partial player-prop subtotals. K/DEF are separately labelled estimates from game lines and historical scoring. Click a number for the calculation and raw quotes. See [Sportsbook projections](Sportsbook-Projections.md).

## NFL role and actual starts

The role label uses ESPN's current depth-chart ordering. The percentage next to it is Qwen's subjective estimate for the upcoming game, not an official NFL probability. An injury designation can override the displayed availability. A starter label does not guarantee snap volume.

**Started** uses ESPN's completed regular-season game roster entries: `starter: true` counts a start; `valid: true` and `didNotPlay: false` count an appearance. The percentage is starts divided by appearances in the current season across all 32 NFL teams, including previous teams for traded players. Future games, preseason, incomplete rosters, and DNP records do not contribute. A missing or incomplete source stays unknown. Team defense is not applicable. Kickers may have appearances with zero official opening-lineup starts.

Profile identity matches require the player's normalized name, NFL team, and position. ESPN roster data, game schedules and completed game rosters are cached. No Yahoo browsing was added.

## Health and privacy

Overview starts with three color-coded boxes: healthy/no injury flags, injury/availability flags, and players whose current Qwen play probability is below 50%. The model category can overlap the injury category. Missing or stale forecasts are never counted as predicted absences; unverified profiles are excluded from the healthy count. Each populated box opens those players' dossiers. A fact-based weekly matchup commentary follows. Research now starts with **Make the next move count.**

Public league views show fantasy **team names**, including the current opponent. Manager names, emails, league IDs, raw Yahoo sections, sessions and operational events remain private. Public enrichment contains only NFL-player/source information. Public JSON response caching is limited to explicitly public read-only routes; the owner dashboard/session routes do not share that response cache.

## Look and interaction

Smoked charcoal, hot amber and restrained orange form the shared palette, with Apple system typography (SF on supported Apple devices, Helvetica/Arial fallbacks elsewhere). Copy uses original playful football language. Motion includes entrance, hover, status and modal transitions, with `prefers-reduced-motion` respected. Native modal dialogs support keyboard focus containment, Escape, backdrop close and return focus. Tables have position filters; mobile cards keep player identity first. Refresh diagnostics are expandable rather than dominating the page.

## Release and rollback

Code repository: https://github.com/ramideltoro/fantasy_football_edge.

Deploy uses the existing dedicated Docker Compose service on backend loopback port 3102. The initial source archive and a verified PostgreSQL custom-format backup are retained under `/var/backups/fantasy-football-edge/game-day-20260918/`. Existing environment values and database volumes are preserved. The Mac worker's previous source is retained in its private support directory as `ai-worker-before-game-day-20260918.ts`. No shared Ollama model/service defaults, passwords, Yahoo write permissions, or other VPS applications were changed.

Validation includes the production build, 51 automated tests locally and in the production image with the test directory mounted read-only, desktop and phone browser checks, real player photos/start records, public privacy checks, instant trade comparison, multi-player comparison, and real model-generated forecasts. Future forecast accuracy remains unproven.
