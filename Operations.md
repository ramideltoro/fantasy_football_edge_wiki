# Operations

## Backend

Backend VPS: `65.75.201.18`. Replacement checkout: `/opt/fantasy-football-edge-v2`. Docker Compose project: `fantasy-edge-v2`. Web: loopback `3102`; PostgreSQL: internal Docker network only. Caddy now serves `fantasy.ramideltoro.com` over HTTPS with upstream 3102. Original deployment at `/opt/fantasy-football-edge` and loopback `3100` is retained for rollback. Caddy backup: `/etc/caddy/Caddyfile.before-edge-v2`.

Store secrets in root-readable `.env`; never log `docker compose config`, environment values or bearer tokens. Environment variables: `DB_PASSWORD`, `IMPORT_TOKEN`, `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`. The Google redirect URI must be exactly `https://fantasy.ramideltoro.com/auth/google/callback`.

```sh
cd /opt/fantasy-football-edge-v2
docker compose -p fantasy-edge-v2 up -d --build
curl -fsS http://127.0.0.1:3102/healthz
docker compose -p fantasy-edge-v2 logs --tail=50 web
```

Deployment procedure: archive the previous source and database, build and test the replacement on 3102, validate authenticated imports and privacy, then update only the fantasy site's Caddy upstream and validate/reload Caddy. Do not modify other VPS services. Rollback changes the fantasy upstream back to 3100 and starts the original container if needed; preserve the v2 database for investigation.

## Mac schedule

`npm run install:importer` writes `~/Library/LaunchAgents/com.ramideltoro.fantasy-football-edge.import.plist`. It starts at login and checks every 60 seconds. The installed application lives in `~/Library/Application Support/FantasyFootballEdge/app`, independently of the development checkout. Normal runs are throttled to hourly. Imported kickoff windows and conservative Sunday/Saturday/evening football windows shorten the interval to 15 minutes. The Mac must be awake, online and logged into the user session.

A PID lock prevents overlapping runs and recovers stale process locks. A failed run does not replace the last snapshot. Local status and logs are in the private importer directory. No automatic team changes occur.

```sh
launchctl print gui/$(id -u)/com.ramideltoro.fantasy-football-edge.import
npm run import -- --force
npm run login:yahoo
```

## Backups and retention

Back up the dedicated PostgreSQL volume with `pg_dump`, encrypt or keep backups private, and verify restoration. Never use `docker compose down -v` during ordinary deployment. Snapshot history grows with imports; establish a retention/export policy before multi-season scale. Server sessions expire automatically. The public UI only receives bounded historical results.

The Google owner login was verified end-to-end after registering the fantasy callback on the existing Google OAuth client, preserving its other redirect URLs. Public JSON was checked independently without the owner cookie.

The importer retries a failed page load once and bounds a run to approximately ten minutes. Local status records the page group being read, while failed uploads preserve the last good server snapshot. The importer now uses ordinary visible Chrome with its dedicated local profile, leaves browser resource loading intact, and spaces page navigations at least five seconds apart. The launch agent uses Standard process scheduling. A Chrome window may appear during imports; avoid closing it mid-run. A private PostgreSQL custom-format backup was created after cutover and its archive catalog was verified under `/var/backups/fantasy-football-edge`.

Yahoo HTTP 429 or 999 stops a run immediately and sets a private `retry-after` timestamp. The scheduler waits at least one hour, or longer when Yahoo specifies a later Retry-After. Even `--force` respects this cooldown; it does not bypass an access block. Player filter readiness checks attachment because Yahoo can hide its native select control. Last complete data remains available throughout failures.

A successful refresh means a newly saved, complete Yahoo snapshot. Incomplete coverage returns HTTP 422 and retains previous data. Duplicate uploads do not fulfill refresh requests. The UI's shared Yahoo data banner is the freshness authority for roster, waiver list, recommendations and league views. A queued request, worker check-in, or dashboard reload is not evidence of fresh Yahoo data.

Interrupted imports now checkpoint successfully parsed pages locally (private `checkpoint.json`) and resume them for up to two hours with the original capture-start timestamp. Checkpoints are scoped to importer configuration and removed after a successful upload. This avoids downloading completed pages repeatedly after transient page failures. No incomplete checkpoint is published. A two-hour-old checkpoint is discarded and collected fresh.

## ZIP snapshot uploads

The Mac worker now sends one `application/zip` request per completed refresh to `/api/import/snapshot`. The archive contains exactly `snapshot.json` with the entire roster, pool, league sections and coverage. Lightweight heartbeat/progress messages remain separate to power live operation logs. The backend reads the archive in memory, rejects extra entries, encryption, malformed JSON and packages exceeding 8 MiB compressed or expanded, then applies the normal schema/completeness checks and atomic transaction. Legacy JSON uploads remain accepted for compatible adapters. The importer remains paused after the network investigation; this deployment does not resume Yahoo browsing.

## Reduced Yahoo browsing scope

At the owner's request, imports now collect only the first two pages (up to 50 players) of **available W/R/T** players, sorted by current-week projected points descending. The separate unfiltered player-list page and full O/K/DEF pagination are removed. Roster and league sections still refresh. Five-second minimum navigation spacing is retained, exceeding the requested one-second delay to avoid increasing traffic. Coverage `players-WRT-top2` means this bounded scope completed, not the entire Yahoo player pool. The ZIP replaces the previous pool rather than presenting old unrefreshed players as current. Checkpoint identity changes with this scope.

## AI worker operations

`importer/install-ai.ts` installs the independent minute-based Qwen worker. Private files `qwen-ssh-key`, `ai.lock`, `ai-status.json`, `ai.log`, and `ai-error.log` live under the Mac's FantasyFootballEdge support directory. Cloudflare service-token credentials are read from the central credentials file and passed only through the SSH process environment. Requests are bounded to four minutes, and abandoned claims become available after ten minutes. Failed research or model jobs expose an owner Retry analysis button. Forecast generation and Yahoo snapshot ingestion are independent; a Qwen outage does not stop roster refreshes.

Backend endpoints: public `GET /api/intelligence`, owner/same-origin `POST /api/intelligence/retry`, worker-token `GET /api/ai/work` and `POST /api/ai/result`. Public responses contain own-team/public player evidence, not private league-page text. Source cache and prediction records persist in PostgreSQL.
