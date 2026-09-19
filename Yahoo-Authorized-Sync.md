# Yahoo authorized sync — implementation and access blocker

Updated September 19, 2026 UTC.

## Current production state

The portal now includes an owner-only **Yahoo connection** panel under Operations. It uses the existing Google owner session, Yahoo authorization-code flow, a one-use state bound to that session, encrypted server-side access/refresh tokens, and automatic token renewal. The Yahoo callback is `https://fantasy.ramideltoro.com/auth/yahoo/callback`.

**Migration is not complete. Do not remove the Mac application yet.** Both centrally stored Yahoo applications were tested through real Yahoo authorization. Authorization succeeded, but league discovery was denied. The currently configured app is **fantasy-2**, Yahoo app ID `zW1RfyJX`, using the centrally named `YAHOO_LEGACY_*` credential pair. Its developer page lists Fantasy Sports Read and the correct callback. A live official API request returned HTTP 403 with:

> This application is not authorized to perform this action.

This is an application/data permission failure after successful OAuth, not a missing portal login or a bad callback. No authorized league snapshot has been retrieved or saved. The last browser-imported snapshot remains intact. Yahoo API access must be provisioned for this application; user consent alone does not establish API access. Application: https://sports.yahoo.com/developer/access/.

## Implemented behavior

- OAuth begins at `/auth/yahoo`; the owner must sign into the portal first.
- Tokens are encrypted with AES-256-GCM under `YAHOO_TOKEN_KEY`, stored separately in the private server environment. Tokens and client secrets are never returned by status endpoints.
- Authorized league discovery selects the existing imported league/team when present, or the only available league; otherwise Operations offers a league selector.
- Server sync runs every 15 minutes after the first successful import. A database advisory lock prevents overlapping scheduler runs. Refresh requests respect Yahoo cooldowns.
- Data adapter reads roster, scoring settings, standings, scoreboard, recent transactions, draft results, team schedule, and a bounded available-player pool (50 W/R/T, 25 QB, 25 K, 25 DEF, ranked by Yahoo overall rank).
- Yahoo player/team/league IDs preserve the existing identifier format. Missing projections/started percentages remain missing; browser-only research pages and current-week projection-sorted pool parity are not claimed.
- Snapshot and forecast writes are atomic. Missing required resources and authorization failures retain the last complete data.
- Browser uploads are rejected only after a successful API cutover. Before that, a denied API connection leaves the legacy refresh request and importer usable.
- Approval/reconnect errors stop scheduled API attempts. Once Yahoo provisions access, use Reconnect Yahoo to reauthorize and retry.

## Verification

101 automated tests passed, including authenticated token encryption/tamper checks, singleton XML-derived structures, an end-to-end adapter fixture, ID and scoring mapping, failure classification and refresh-token preservation. TypeScript and Vite builds passed. Production container tests passed before deployment. Live OAuth callback succeeded for both configured apps. Unauthenticated status is 401, a cross-origin league change is 403, and an invalid callback state is 400. Production health checks remain successful; database confirms `approval_required`, no successful API sync, and encrypted token storage.

League-data mapping has fixture coverage but **cannot yet be validated against this owner's live API data**. After approval, compare roster, scoring, standings, matchup, pool, field availability and kickoff locks against Yahoo before declaring migration complete. Verify token refresh, restart recovery and at least one scheduled sync.

## Mac removal prerequisite

The Mac support directory also runs the Qwen AI queue worker (`com.ramideltoro.fantasy-football-edge.ai`). Yahoo API sync removes the browser importer dependency only. Before deleting the whole application, migrate that AI worker to the existing AI host or another always-on host and verify real job completion. This deployment does not move or disable that worker, delete the application, or stop the functioning browser importer.

## Deployment and rollback

Backend: `/opt/fantasy-football-edge-v2`, Compose project `fantasy-edge-v2`, loopback port 3102. New private environment settings: `YAHOO_CLIENT_ID`, `YAHOO_CLIENT_SECRET`, `YAHOO_TOKEN_KEY`. Preserve the token key across rebuilds. Existing Google login and database remain in place.

Pre-release source archive, database dump and restore catalog: `/var/backups/fantasy-football-edge/yahoo-oauth-20260919/`. Build/test logs are retained there. Restore the archived source and rebuild web for code rollback; the additive Yahoo tables can remain. Do not restore an old whole database merely to undo this feature because it would discard later imports.
