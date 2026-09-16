# News intelligence and modular dashboard

Released September 16, 2026. News affects advice, not numerical point forecasts. Yahoo points, statistical forecasts and Qwen's subjective 0–100 waiver priority remain separate. No Yahoo transactions or betting actions are performed.

## Collection

A single VPS collector replaces both prior collectors. It checks ESPN, Yahoo, CBS, FantasyPros, three Draft Sharks feeds and Reddit r/fantasyfootball every 30 minutes. Google News searches cover roster players first and up to two eligible waiver candidates per position, capped at 30 requests per rolling hour. This runs independently of Yahoo browsing and adds no Mac browser traffic.

Feed requests use ETag/Last-Modified, a 12-second deadline and at most two concurrent downloads. Failing sources back off from 30 minutes to six hours. Cached articles remain available with source health and publication dates. Search listings are discovery only: encoded links, headlines, or repeated titles cannot establish events.

Up to 12 matching public publisher pages per cycle supplement short excerpts. Only ESPN, Yahoo, CBS, FantasyPros and Draft Sharks hosts are accepted; redirects and blocked pages are not followed. JSON-LD articleBody or article/main paragraphs are extracted, not navigation or scripts. Enrichment attempts are cached for four hours. No paid feeds or paywalls are bypassed.

Articles retain source, URL, publication and retrieval time. Tracking parameters are removed and normalized matching titles deduplicate syndication. Identity must appear in actual text; a search label is never evidence. Defense matching requires a full NFL team name and defensive/matchup context. Extraction isolates sentences about the player so another player's injury in a roundup is not attributed to them. Reporting, opinion and community discussion stay distinct. Topic tags identify relevant context, not official confirmation.

## Events and Qwen

Events cover injury, practice, availability, role, workload, transactions and matchup context. Each carries a substantive excerpt and source. Corrections supersede older event versions. Parser version 4 supersedes initial validation records containing encoded markup or overly broad attribution; those records are excluded from the portal.

The Mac claims one changed-player batch at a time, prioritizing roster players. Evidence hashes include season, week, player context and cited events. Unchanged evidence reuses prior work; obsolete pending versions are superseded. News does not require a new Yahoo snapshot. Each batch contains up to four events and available ESPN matchup context.

Qwen selects an event, exact supporting quote and cautious action. Worker and backend reject unknown IDs, fabricated quotes, incomplete responses and markup used as evidence. Published interpretations use constrained language beside the quote, not unchecked generated factual prose. Community/opinion events, reports older than 48 hours, locks and unavailable players are monitor-only. Current snapshot eligibility is checked again on save.

Success stores timestamps, citations and the change from the previous action, then makes the latest roster/waiver analysis eligible again. Evaluated events are included in that prompt. News never writes point projections. Previous advice remains dated while new evidence is pending.

NutsNews and the shared qwen2.5:3b configuration are unchanged. The Mac PID lock permits one inference request at a time. Four-minute request bounds, ten-minute abandoned leases and three automatic attempts remain. The minute-based LaunchAgent drains batches while the Mac/server are online; first collection can queue several batches. An expired final attempt becomes failed instead of remaining analyzing.

## Storage and APIs

Additive tables: `news_sources` (cache/conditional headers/backoff), `news_articles` (deduplicated content), `player_events` (versioned cited evidence/assessments), `news_runs` (immutable input, evidence version, priority, attempts and results), `news_state` (phase/counters/heartbeat/market) and `news_searches` (hourly budget).

Articles and player-event rows expire after 90 days. Analysis inputs/results retain historical citations and timestamps. Current collection uses seven-day evidence.

- `GET /api/news`: phase, source health, counts, recent runs/events, discovery headlines, score history and market context.
- `POST /api/news/refresh`: owner session and same-origin required. Reuses collection in progress, respects cooldowns and retries failed work; never invokes Yahoo browsing.
- `GET /api/players/:id/news?page=0`: 20 events per page, hasMore and saved Qwen score history.
- `GET /api/ai/work`: existing worker-token endpoint, now optionally returns kind=news; otherwise existing roster/six-position analysis.
- `POST /api/ai/news-result`: worker-token endpoint; validates and saves atomically.
- `GET /api/intelligence`: existing fields retained, plus news summary.
- Existing `/api/ai/progress` heartbeats and owner-only logs continue.

Free ESPN scoreboard data supplies matchup dates and odds when present. Rows include source/time; absent spreads or totals are unavailable. Team totals do not become player forecasts or win probabilities.

## Interface

Primary destinations: Overview, My Team, Waivers, Research and League; Operations is secondary. My Team combines roster, lineup decisions and team analysis. Research contains news and player research.

Overview leads with the matchup when available, lineup gain, a short decision queue and news since the previous visit. Waivers has six position cards with separate Qwen/Yahoo/statistical values, expandable rationale and comparison charts/tables. The full briefing and complete player table remain accessible.

The shared player drawer contains historical points, imported points with publication-time markers, score history and paginated evidence. Markers show timing, not causation. Missing history stays empty. Research filters events and separates discovery headlines. Operations holds source diagnostics, phase counters, batch history and owner-only live AI/Yahoo logs.

A compact freshness strip separates Refresh news & AI from Refresh Yahoo. Reads are cached/deduplicated. Amber accents, Inter typography and consistent cards organize the portal. Panel transitions last 180 ms; reduced motion disables them. Charts do not continually animate during polling. Drawers retain keyboard focus and lock background scrolling.

## Validation and recovery

Tests cover RSS/Atom, stale/future/unsafe items, encoded HTML, search exclusion, ambiguous names, defenses, multi-player attribution, corrected evidence, publisher extraction, exact citations, locks, opinion and unchanged point forecasts alongside existing importer tests.

For a stalled run, inspect Operations and private Mac ai-status.json. Collection heartbeats and AI inference are separate. Owner Refresh news & AI retries failed work while preserving past results. Do not change credentials or NutsNews. Rollback: redeploy application commit 89f90e3 and sync that Mac worker; additive tables may remain for later recovery.

### Deployment verification — 2026-09-16

Application commit `e254460` is deployed on the backend VPS and synchronized to the Mac worker. All 44 tests and the production TypeScript/Vite build passed. Desktop and 390-pixel mobile checks covered navigation, candidate comparisons, cited news, player drawers, Escape/focus restoration and the refresh/status flow. Reduced-motion styling is supported and chart polling does not animate.

The live collection reported 37 healthy source entries (eight feeds and 29 discovery searches), with 1,211 deduplicated articles/discovery listings. These are not all verified player events: strict evidence checks retained three current topic events across two players. Qwen completed cited assessments for Tyler Loop and A.J. Brown; older evidence is labeled for monitoring. ESPN supplied market context for 16 matchups. Missing matchup projections remain unavailable.

Owner-triggered news refresh returned the queued/reuse notice without changing the Yahoo snapshot. An unauthenticated refresh returned HTTP 403. News jobs drained and the subsequent roster/waiver Qwen analysis completed. Historical superseded validation runs remain visible for audit, separate from current evidence.

NutsNews/local AI health was healthy after deployment, with its service start time and qwen2.5:3b configuration unchanged. Yahoo pacing, player-page limits and single-ZIP uploads were preserved.
