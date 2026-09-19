# Game plan, kickoff watch and receipts

The September 18, 2026 release adds eight connected features. After the September 19 navigation cleanup, Overview shows only roster health, the weekly matchup and commentary; detailed controls live on My Team, Waivers and Research. Tabs have shareable URL fragments and support browser Back/Forward. The amber palette, Apple system typography, player dossiers and sortable tables remain consistent.

## Where to find each feature

| Feature | Location | What it does |
| --- | --- | --- |
| Kickoff availability alerts | My Team → Kickoff watch; health summary and a kickoff link on Overview | Highlights flagged starters, eligible healthy direct bench replacements, source times and lock countdowns. |
| Projection report card | Research → Past results → Projection accuracy | Grades saved Yahoo, Qwen and combined full forecasts against final fantasy scores, with position filters, mean absolute error, bias and common-game comparisons. |
| Three-week roster planner | My Team → Three-week plan | Shows opponent schedules, byes, unresolved injury/data flags, lineup gaps and eligible imported pickups. |
| Since-last-visit feed | Research → News & analysis → What changed | Material health, depth-chart, projection, roster and waiver-pool changes with source and observation times. |
| Risk preferences | My Team → Set lineup | Choose most expected points, protect the lead or swing for the fences, alongside the four projection sources. |
| Multi-week waiver impact | Waivers → Pickup impact | Choose an outgoing roster player and available incoming player; opens their comparison and shows before/after lineups, score changes and lost position depth. |
| FLEX timing and injury contingency | My Team → Kickoff watch | Suggests later starters in flexible slots while keeping the same starters, plus earlier decision deadlines and later bench backups for questionable starters. |
| Weekly recap | Research → Weekly recap | Roster MVP, largest miss against Yahoo, newly added player, source comparison and evidence-based start/sit receipts. Incomplete weeks are explicitly labeled. |

All recommendations are previews. Apply lineup changes and claims in Yahoo. No Yahoo writes or external messages are sent by this release.

## Availability and notifications

`server/gamePlanService.ts` runs a one-minute worker with a PostgreSQL advisory lock. It checks ESPN roster injury reports for NFL teams represented on the roster every 30 minutes, tightening to five minutes when an owned player's kickoff is within 90 minutes. Yahoo imports keep their existing schedule and cooldowns. The worker, not an open browser, maintains persistent source state.

An explicit unavailable Yahoo designation is never cleared by an absent ESPN report. Fresh ESPN out/IR/PUP/suspension reports can exclude a player from the optimizer. Questionable/doubtful flags produce a watch item. Missing or failed checks do not become proof of health. Source links and timestamps stay visible; official healthy scratches may not appear in an injury feed.

Browser alerts require the user to click **Enable browser alerts** and grant the browser's native permission. They notify for urgent flagged starters within 90 minutes of a known kickoff while the portal page stays open, and deduplicate alerts in local storage. This is not a closed-browser push service, SMS or email. Alerts are withheld when the game-plan state is stale or the underlying Yahoo snapshot is over two hours old. Clicking a notification opens Kickoff watch.

## Forecast ledger and fair comparisons

`gameplan_forecasts` stores the first accepted pregame forecast per league/team/season, week, player, source and method. It is append-only for that key and survives restarts. Unknown kickoffs, locked/completed games, imports over two hours old and snapshots uploaded after kickoff are excluded. This intentionally evaluates the first saved forecast, not a cherry-picked last forecast.

Original historical Yahoo snapshots may be backfilled using their recorded receipt timestamps. Historical Qwen and combined forecasts are not reconstructed with current evidence. New Qwen/combined results accumulate prospectively. Completed player-game scores are required; live scores do not count. Later imported stat corrections can revise the scored result.

- **Average miss (MAE):** mean absolute difference between forecast and final points; smaller is better.
- **Bias:** forecast minus actual; positive means overprediction.
- **Common games:** only player-games with scored Yahoo, Qwen and combined forecasts, separately filterable by position.
- **Bookies:** offensive partial forecasts are graded only against the categories saved with that forecast and the original multipliers. Every matching actual stat must be available. K/DEF specialist models use full final fantasy totals. These stay separate from the full-forecast leaderboard.
- Summary statistics cover all stored observations; the browser receives at most the latest 500 scored receipt rows.

The combined forecast contract remains in [Lineup modes](Lineup-Modes-and-Table-Sorting.md): offensive book props are completed with league-scored historical points from uncovered categories before entering the equal-source average. Raw partial props are never averaged directly with full forecasts.

## Planning and risk assumptions

The current week uses combined forecasts plus actual scores for locked starters. Future weeks use league-scored historical baselines and the confirmed NFL schedule, not reused current-week Yahoo/Qwen/book projections. Current injury flags remain unresolved rather than assuming recovery. Schedule gaps count as byes only with a complete 17-game team schedule; otherwise they are unknown. Missing components keep a whole-lineup total blank. Each player can fill one eligible slot only.

The planner maximizes filled slots first, then estimated points, breaking ties toward fewer moves. It uses a slot-bitmask matching algorithm to keep gap searches fast. Pickup impact repeats that calculation after removing the outgoing player and adding the incoming player. A locked outgoing/incoming player prevents a current-week gain claim. Position-depth counts show the cost of dropping from another position. Dropping an IR player models the incoming player on the bench and notes that Yahoo roster limits may require another move. Future-week pickup availability is today's imported availability, not a guarantee of a successful future claim.

Risk modes need at least five of the last eight available prior games. Each game's fantasy score uses the league's scoring rules. The empirical 25th/75th percentiles are shifted around the selected point forecast by subtracting the historical mean. Protect mode optimizes the lower score; chase mode optimizes the upper score. Expected-point change is still calculated from the original point forecasts, so a safer lineup can explicitly lose expected points. These are historical variation heuristics, not calibrated confidence bounds, floors, ceilings or win probabilities. Prior-season samples are labeled. Players without sufficient history use the selected point forecast. Bookies-only mode disables risk choices because it includes partial category scores.

FLEX suggestions exchange slots only when eligibility works in both directions, both players are unlocked and known kickoff times improve late-game flexibility. They do not change the starter set or claim a points gain. All listed swaps should be applied together. Questionable-player contingency lists distinguish same/later-game bench options from earlier backups requiring an earlier decision.

## Changes and weekly recaps

Changes require an observed baseline; first startup does not fabricate news. Projection moves need at least two points and 20% of the previous value. The feed also records verified role changes, availability flags, newly imported available players with Yahoo forecasts of at least eight points, and roster additions. Source time and detection time are separate. Up to 250 events are retained for 14 days. Last-visit and read markers are scoped to this browser and league/team identity; navigating away from and back to the feed preserves the same visit baseline.

Recaps use recorded roster history and completed scores. The MVP is the highest-scoring recorded roster member, including bench players. The pickup comparison needs a previous recorded week's roster. A weekly source winner needs at least three completed player-games shared by all three full sources. Start/sit receipts use the last recorded pregame Yahoo forecasts and eligible direct bench substitutes; they distinguish a pregame opportunity from a reasonable choice followed by a worse outcome. They do not retroactively claim that a better bench score proves the decision was bad. Full slot combinations and a manager's risk preferences can change the decision.

## Operations

- `GET /api/gameplan`: public, sanitized latest game-plan state, source health, schedule, alerts, changes, report card and recaps. Does not expose private league/manager IDs or the internal observation baseline.
- `GET /api/dashboard`: the same state plus freshly enriched player data, league-scored scoring history and current source availability.
- `gameplan_state`: persistent state keyed by a hash of provider, league, team and season.
- `gameplan_forecasts`: persistent pregame forecast ledger.
- ESPN and schedule data share the existing `research_cache`. The schedule is maintained by the existing research worker.
- Failed updates preserve the last successful state; the next minute retries. State older than ten minutes is visibly stale. No new secrets or third-party notification service is required. Archived roster players retain clickable dossiers, with historical labels and previously stored profile data when available.
- HTML uses revalidation caching so navigation does not retain a previous deployment's entry page; hashed assets retain cache behavior.

Pre-release source and database backups: `/var/backups/fantasy-football-edge/gameplan-20260918/`. Roll back by restoring the source archive and rebuilding/recreating only the web service. The additive game-plan tables can remain; database restoration is not necessary to reverse the UI change.
