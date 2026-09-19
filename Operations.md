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

The importer retries a failed page load once and bounds a run to approximately ten minutes. Local status records the page group being read, while failed uploads preserve the last good server snapshot. The importer now uses headless Chrome with its dedicated local profile, leaves browser resource loading intact, and spaces page navigations at least five seconds apart. The launch agent uses Standard process scheduling. Regular imports have no visible Chrome window. `--login` remains visible for authentication; private config `headless: false` is an explicit diagnostic override. A macOS caffeinate idle-sleep assertion lasts only for an active import and is released on completion or process exit. Screen locking and display sleep remain allowed. The existing user LaunchAgent works while the session is locked, but cannot run while the Mac is asleep, shut down, or logged out; it resumes checking after wake. Keep the Mac awake and connected for scheduled refreshes. A private PostgreSQL custom-format backup was created after cutover and its archive catalog was verified under `/var/backups/fantasy-football-edge`.

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

## Recommendation refresh and outage handling (September 15, 2026)

Recommendations and Waiver list show the Yahoo snapshot time, last successful Qwen time, a refresh icon, and expandable live activity. Owner refresh queues the existing single-ZIP Yahoo importer and requests another analysis where no analysis is already queued/running. A complete import creates its own analysis job. UI polling is shared to avoid duplicate expensive reads; historical accuracy queries omit captured raw page sections.

The Mac posts analysis stages and 15-second heartbeats to a bearer-authenticated endpoint. Owner-only logs show work checks, waiting for Qwen, validating/uploading results, success or connection failure. More than two minutes without a heartbeat is flagged as potentially offline/stalled. A heartbeat indicates process liveness, not inference completion. Existing four-minute inference deadlines and ten-minute abandoned-job leases remain. Failed inference retries are bounded to three attempts per snapshot; owner retry resets that budget. Logs retain the latest 2,000 entries.

Successful analyses are appended to qwen_history. The API returns previousQwen even when the latest snapshot analysis fails or has no result. The UI marks non-complete or over-four-hour Qwen analysis Not updated, preserves its original generation date, and checks displayed candidates against current availability/locks. If no Qwen candidate is available, a labeled statistical shortlist remains. Neither fallback text nor Yahoo fantasy points are presented as generated Qwen scores.

During deployment validation the local-server tunnel and ai.nutsnews.com both returned Cloudflare HTTP 530 and LAN SSH to its documented address timed out. Qwen generation could not be verified while the server was unreachable; the owner was notified. NutsNews and Ollama model/service settings were not changed.

## Local AI restored and request validation tightened (September 15, 2026)

The Cloudflare SSH route and Ollama are reachable again. NutsNews health reports its original qwen2.5:3b default; no shared model or service settings were changed. The fantasy worker continues to call that model through its existing authenticated SSH route.

Live verification exposed invalid model output: the old unconstrained position list could repeat quarterbacks, associate text with the wrong ID, and cite nonexistent news. Such output was rejected rather than published. The request now uses a required object for each supplied position, candidate-specific ID and news-index constraints, and a compact evidence context. The Mac validates complete position coverage before uploading; the server still checks IDs, availability and evidence. Scores are integer subjective priority ratings out of 100, separate from projected fantasy points. The output allowance is 2,400 tokens, within the existing 8,192-token context and four-minute request deadline.

The last raw response is overwritten in the private Mac support directory as `ai-last-response.json` (mode 0600), never committed or exposed through the portal. `ai-status.json` records a bounded diagnostic reason. Truncated output is rejected explicitly. Last successful advice remains visible during failures and retries. Automated validation now includes missing positions, cross-position IDs and nonexistent news citations (35 tests total).

Explanations that treat coverage counts as an advantage, or invoke absent news, now use the labeled evidence-summary fallback. The score prompt defines a 0–100 priority rubric rather than leaving the scale implicit. This does not constitute calibrated predictive accuracy.

Live recovery verified at September 15, 2026, 11:36:52 PM EDT: latest snapshot analysis is complete, qwenUpdated=true, all six position selections have generated ratings and three player insights were saved. The final inference took 120 seconds (4,377 prompt tokens, 974 output tokens). A prior constrained run also succeeded in 105 seconds. Some Qwen prose remained unsupported—including interpreting a waiver date as game availability—so the portal substitutes explicitly labeled evidence summaries while retaining model selections and ratings. These checks establish working integration, not forecast accuracy.

## News intelligence and modular portal (September 16, 2026)

See [News Intelligence](News-Intelligence.md) for the consolidated 30-minute VPS collector, source backoff/search limits, evidence validation, AI batch queue, additive APIs and redesigned navigation. News & AI refresh is independent of Yahoo refresh. Source diagnostics and worker logs are in Operations; Yahoo page pacing and the single-ZIP upload remain unchanged.

### VegasInsider odds desk

The backend now refreshes the complete NFL odds boards every six hours, with a persistent due time and automatic retries. Check `/api/sportsbook` for health and `/api/sportsbook/board` for the normalized source data. See [Sportsbook projections](Sportsbook-Projections.md) for the calculation, failure behavior and authorized refresh endpoint.

### League-scored Qwen release

Current numerical jobs use `qwen-points-v5`; research version 12 supplies the scoring baselines. `/api/projections` reports queue coverage and prospective accuracy for this method only. Legacy v4/statistical jobs remain persisted but cannot fill the new Qwen column. Deployment recalculates progressively in six-player batches using the existing Mac worker; ready roster/K/DEF batches are prioritized, and four-hour freshness rules still apply. No shared Ollama configuration change is required.

Pre-release source/database backup: `/var/backups/fantasy-football-edge/calibration-20260918/`. Build and container test logs are retained there. Run the runtime container's test command with the repository `tests` directory mounted at `/app/tests:ro`; the production image intentionally excludes tests. See [League scoring and explanations](League-Scoring-and-Explanations.md) for calculations and verification.

During the final backfill, the shared resident Qwen model had a 1,024-token context. Small inference requests succeeded while the worker's existing 8,192-token requests timed out; `/api/ps` alone did not establish inference health. Reloading the same model at 8,192 tokens restored inference temporarily (about 2.5 seconds for the probe), but another consumer subsequently loaded the smaller context again.

The fantasy worker now uses a dedicated **fantasy-qwen.service** on the local AI host, bound only to `127.0.0.1:11435`. Its unit is versioned in the application repository at `deploy/fantasy-qwen.service`. It runs the existing Ollama binary as the existing `ollama` user and reads the installed model directory read-only. It keeps an 8,192-token context, one parallel request, one loaded model, a queue limit of 16, lower CPU priority and an 8 GiB memory ceiling. The loaded 3B model uses approximately 2.4 GB. No additional model download, public listener, credential, or change to the shared port 11434 service is required. The unit is enabled across host restarts.

The private Mac `config.json` sets `ollamaPort: 11435`; `importer/ai.ts` validates this integer and retains 11434 as the default for other installations. The existing SSH transport, bearer authorization, model name, context/output limits and API result contract remain unchanged. Private pre-change config/worker backups are retained in the FantasyFootballEdge support directory. The worker LaunchAgent was resumed after deployment; superseded inputs were retired and timed-out current jobs were requeued. Rejected outputs were never published.

For rollback, restore the Mac worker/config backups and disable the dedicated fantasy unit after confirming no fantasy inference is active. Do not remove the shared model directory or change the other applications' endpoint. For diagnostics, check `systemctl status fantasy-qwen`, the private runtime's `/api/ps`, and a bounded real inference request; list/health endpoints alone are insufficient.

### Scouting and league lab

`GET /api/edge-lab` serves persistent, sanitized scouting state. A minute worker recomputes on a changed snapshot or after 30 minutes; external statistical sources have six-hour caches, ESPN weather one hour, and ADP one day. Matchup residuals are cached by position/source context and computation yields between player/trade evaluations. Last successful state survives worker failures and container restarts. See [Scouting and league lab](Scouting-and-League-Lab.md).

The Mac's extra league roster/schedule reads are cached for six hours and keep existing Yahoo pacing/cooldowns. The installed importer script was updated and a complete 35-page import verified. Official API scouting is optional so unavailable resources do not invalidate an otherwise complete core snapshot. Median-rule uncertainty withholds playoff scenarios.

Rollback backup: `/var/backups/fantasy-football-edge/scouting-20260919/`. Restore its web source/image and recreate only the web service. The additive `edge_lab_state` table need not be dropped. The prior browser importer is also present in the source backup if the added scouting reads need to be reverted.
