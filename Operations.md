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

The importer retries a failed page load once and bounds a run to approximately ten minutes. Local status records the page group being read, while failed uploads preserve the last good server snapshot. Image/media/font requests are skipped in the dedicated headless importer to reduce unnecessary loading. A private PostgreSQL custom-format backup was created after cutover and its archive catalog was verified under `/var/backups/fantasy-football-edge`.

Yahoo HTTP 429 or 999 stops a run immediately and sets a private `retry-after` timestamp. The scheduler waits at least one hour, or longer when Yahoo specifies a later Retry-After. Even `--force` respects this cooldown; it does not bypass an access block. Player filter readiness checks attachment because Yahoo can hide its native select control. Last complete data remains available throughout failures.
