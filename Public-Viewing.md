# Public viewing and dashboard loading

October 5, 2026: the portal can be browsed without an owner session. Overview, roster, lineup decisions, waivers, league standings, trade/pickup tools, research and player dossiers use the public read APIs. Header/sidebar owner sign-in links and the League sign-in card have been removed. Manager identities, raw private Yahoo captures and account identifiers remain excluded from public snapshots.

Administrator access is confined to the collapsed **Operations → Worker activity → Administration · connections & manual refresh** section. Yahoo account connections, manual data refreshes, worker logs, import tokens and authenticated writes retain their server-side checks. Viewing the portal does not need this access.

## Root cause and fix

The reported login wall was also masking a data-loading failure. The main dashboard request exceeded its 20-second browser timeout. The UI then incorrectly displayed the initial setup card with an owner sign-in button. Errors now show a public retry state; a genuinely empty database shows a separate waiting-for-roster state, without a login button.

Historical queries were reading hundreds of complete free-agent pools and research payloads. The dashboard history cache also expired from request start, so a slow query could launch additional copies before the first completed. Changes:

- `server/historyQueries.ts` expands stored JSON documents once with PostgreSQL `jsonb_to_record`, selecting only fields required by each historical calculation. The dashboard keeps its original latest-1,000 ordering and scope. Accuracy keeps the original first-1,000 ordering, completed-result filters and league/team/player keys. Real zero and negative scores remain valid.
- Historical reads share one pending promise and cache results for one minute after completion. Cache keys include query and parameters; failures can retry. No session response or credential is cached here.
- The game-plan service retains full original-Yahoo backfill behavior. After that one-time backfill, only completed free-agent outcomes are needed; their full stats and all own-roster history remain available to grading and recap calculations.
- Legacy statistical and Qwen accuracy reports refresh in the background every 15 minutes. Current player data and recommendations return without waiting for these audits. Existing results remain visible while updating. Initial loading and failures are explicit, rather than fabricated zero-sample reports. Failures retry after one minute.

The dashboard also uses the depth-chart collector asynchronously. On a cold cache, NFL roles remain explicitly unconfirmed until fresh source data arrives; reading the roster no longer waits for requests to all 32 NFL teams.

## Validation and rollback

The TypeScript/Vite build and all 138 tests pass, including slow-read deduplication, completion-based expiry, cache isolation, background loading, retained results and retry behavior. Temporary PostgreSQL fixtures verified all new query forms, completed-only outcomes including zero/negative scores, matching Qwen baselines and unchanged own-roster records. These fixtures are rolled back and do not alter live records.

Rollback backup: `/var/backups/fantasy-football-edge/public-access-20261005/` contains prior source, a database dump and the previous container image reference. No database schema or authentication policy migration is required.

Production validation for application commit `271445f`: a new browser context with no owner cookies displayed the health board, coach read, roster, waiver rows, league standings, news and team analysis. All ordinary viewing routes contained no Google/owner sign-in links. The initial signed-out Overview rendered in 16.8 seconds in the final check; this is not a claim of instant cold loading. Desktop and 390 px mobile checks passed without runtime errors or horizontal overflow. Simulated 503 and empty-import responses both offered public recovery controls with no login link. Anonymous write attempts to import refresh, Yahoo disconnect, analysis retry and sportsbook refresh each returned 403. Public snapshot checks confirmed 17 roster players and 785 historical snapshots without raw private sections or league identifiers. All 138 tests also passed in the production image.
