# User guide

Start on **Overview** for three things: “Who’s ready to rumble?” (roster health), this week’s matchup scorecard with both team names and actual/live projected points, and the coach’s locker-room read. Health counts and player names open their dossiers. The read judges the current numbers against your opponent’s completed games, uses a louder coach’s voice, and updates as inputs change. Expand **Open the full scouting report** for playmakers, injury context and sortable game receipts. Phrasing refreshes every 30 minutes; source timestamps stay visible. The lineup and kickoff links take you to the detailed decisions. A dash means a score or forecast is unavailable, never zero.

The main sections are **Overview, My Team, Waivers, League, Research** and the **Operations** utility. Each tool menu groups related pages under a purpose, such as “This week,” “Plan ahead” or “Past results.” Existing bookmark URLs still work. Repeated banners, action tiles and the change feed no longer fill Overview.

## My Team

- **Roster (This week):** compare selected players, inspect NFL role, your fantasy slot, Yahoo/Qwen/bookies projections, rostered percentage and actual NFL season starts. Check two or more players to open a side-by-side comparison. Expand **Weekly scoring breakdown** below the roster for “Who’s carrying the cooler?” and the original-versus-live matchup chart. It starts closed.
- **Set lineup (This week):** choose Yahoo only, Qwen only, Bookies only or All combined. Then choose most expected points, protect the lead or swing for the fences. The suggested lineup respects eligibility and game locks. Expected-point tradeoffs, missing sources and historical-range sample counts are explicit. Bookies mode uses partial/category scores and disables full-score risk preferences.
- **Three-week plan:** see upcoming opponents, byes, injury/data flags, lineup gaps and pickup options. Future weeks use historical planning baselines, not future-week model forecasts.
- **Kickoff watch:** review flagged starters, direct bench replacements, lock countdowns, source checks, FLEX timing moves and late-game contingencies. Browser notifications are opt-in and require the page to remain open; the server continues checking independently.
- **Matchup radar (Plan ahead):** review upcoming defensive matchups, the next three opponents and available venue/weather context.

## Waivers

**Find players → Available players** uses the same scouting table and dossiers as your roster. Filter by position and availability; sort any column. Click Qwen points or start probability for the explanation. Sportsbook figures show their partial coverage or specialist-model labels and open the underlying quotes and math.

The **Waiver shortlist** is collapsed above Available players. Open it for up to three quarterbacks, followed by the best other-position candidates. **Breakout radar**, in the same Find players group, highlights targets, carries, red-zone usage and workload changes.

**Plan a pickup → Pickup impact** asks for an outgoing roster player, then an available incoming player. Selecting both immediately opens the player comparison. Close it to inspect three weeks of before/after lineups, projected changes, gaps and position depth sacrificed. The candidate list starts collapsed. These are previews; make claims or roster moves in Yahoo.

**Plan a pickup → Claim coach** compares next-week roster benefit, opposing roster demand and rolling waiver priority.

## Research

- **Past results → Projection accuracy:** compare Yahoo, Qwen and combined forecasts against final results, filter by position and restrict comparisons to shared player-games. Lower average miss is better. Sportsbook partial forecasts are evaluated separately. New model samples accumulate after the saved forecasts’ games finish.
- **Past results → Weekly recap:** choose a week for the roster MVP, largest Yahoo miss, pickup spotlight and start/sit receipts. A better bench outcome alone does not prove a bad pregame choice. In-progress weeks are marked.
- **News & analysis → What changed:** filter material health, role, projection, waiver-pool and roster updates since your last visit in this browser. “I’m caught up” resets the marker.
- **News & analysis → News & advice:** next-move recommendations, attributed headlines, source links and news evidence.

- **News & analysis → Team analysis:** Qwen’s broader weekly brief, evidence and player context.
- **Preseason → Draft Room:** ADP, market ranges and the session-local practice board.

## League and Operations

**League → Standings** shows league team names and records publicly while hiding manager identities. **Trade finder** and **Playoff race** sit beside Standings. The current weekly matchup summary is on Overview. Owner sign-in exposes the private imported league pages. **Operations** contains worker activity, Yahoo cooldown/pending state, import health and the expandable source/refresh controls. Every page retains its Yahoo import timestamp and stale-data warning, with a compact **Sources & refresh** link to Operations.

The owner refresh control queues a request for the Mac importer; the reload icon only reloads the dashboard. The Mac must be awake and online to import Yahoo, and existing rate-limit cooldowns still apply. Sportsbook boards refresh independently on the VPS every six hours; kickoff injury checks tighten near games.

All tables can be sorted from their column headers. On narrow roster/waiver cards, use the mobile sort selector. Detailed tables scroll horizontally. A dash means unavailable, not zero. Check source timestamps before acting. No automatic Yahoo lineup changes, trades or waiver claims are submitted.

See [Game plan and receipts](Game-Plan-and-Receipts.md), [lineup modes](Lineup-Modes-and-Table-Sorting.md) and [sportsbook calculations](Sportsbook-Projections.md) for precise methods and limitations.
