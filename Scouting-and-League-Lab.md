# Scouting and league lab

Released September 19, 2026 (UTC). The feature set builds on the existing amber interface, Apple system font stack, sortable tables, player dossiers and four-source lineup recommendations.

## Navigation

- **Overview:** three shortcuts to workload, matchups and trades.
- **Waivers → Breakout radar:** owned, available and imported-league filters; targets, carries, target share, air-yard share, offensive snap share, inside-20 targets, inside-5 carries, workload change, opportunity points and actual points.
- **Waivers → Claim coach:** next-week lineup benefit for each legal bench replacement, opposing roster demand and higher-priority competitors. This league uses continual rolling waiver priority, so the tool does not invent FAAB dollar bids.
- **My Team → Matchup radar:** three upcoming opponents and opponent-adjusted positional scoring context, alongside ESPN venue/weather information. Indoor venue metadata does not establish that a retractable roof is closed. Missing wind/forecast information remains unknown; weather does not automatically change projections.
- **My Team → Lineup & decisions:** pin a preferred starter or exclude a player, with the existing Yahoo/Qwen/Bookies/combined and risk controls. Impossible combinations return no complete lineup. Game locks remain authoritative.
- **League → Trade finder:** automated one-for-one candidates that improve both projected starting lineups; manual trades of up to three players per side, including two-for-one. Selecting a proposal fills the editor. Player comparison opens the shared dossier modal.
- **League → Playoff race:** current-week optimized totals, next-week baselines and experimental regular-season qualification scenarios using the actual remaining schedule, including this league's extra median result.
- **Draft Room:** free ADP, market bands, observed draft-position availability, notes, mark-taken/undo/reset controls and player dossiers. This is a session-local practice board, not an automated live draft assistant. It does not submit picks or provide auction/keeper valuations.

## Free sources and collection

`server/edgeLabService.ts` maintains additive `edge_lab_state` records, scoped by a hash of league/team/season. A minute worker checks for a new snapshot or a state older than 30 minutes. Computation yields between player and trade evaluations so the HTTP event loop can continue serving requests. The public read-only endpoint is `GET /api/edge-lab`.

- nflverse current/prior-season weekly player and team stats, snaps, and schedules: cached for six hours in the existing PostgreSQL `research_cache`. https://github.com/nflverse/nflverse-data
- Current-season nflverse play-by-play: downloaded compressed, reduced to game-coverage markers and relevant red-zone fields before persistence. Counts remain unavailable without a matching player identity, covered game and fresh successful source. Historical seasons are not fetched for this feature.
- Fantasy Football Calculator ADP: daily cache; scoring format follows imported reception scoring and team count follows league standings. API is documented as free for personal/commercial use, with attribution requested. UI links the provider and shows sample dates/counts. https://help.fantasyfootballcalculator.com/article/42-adp-rest-api
- ESPN scoreboard: hourly cache of venue, kickoff and available weather context. This is an undocumented public feed without an availability guarantee. No paid weather service is introduced.
- Existing VegasInsider collection remains on its six-hour schedule with its prior coverage, partial-projection and lock contracts.

A failed fetch retains its original timestamp and last successful data, with source failure metadata. The scouting UI flags stale snapshot state and failed source refreshes. Dataset attribution and upstream source licenses remain applicable; no competitor subscription content is copied.

## Yahoo scouting and privacy

The Mac importer reads all league rosters and each team's schedule. It discovers roster links from imported standings, preserves the existing navigation pacing and Yahoo cooldowns, and caches the additional scouting pages for six hours. The first successful full scouting import contained all 12 teams, 35 total pages and 118 available players. Everything travels in the existing single ZIP snapshot; cookies remain on the Mac.

The official Yahoo adapter also accepts optional normalized `leagueRosters` and captures league settings needed for scouting. Optional scouting failures do not invalidate a complete core API import. Median scoring must be explicitly known before probabilities are calculated. The recent Yahoo authorization and browser-migration safeguards remain intact.

The public scouting response includes player information and team names, but no raw Yahoo sections, manager identities, access tokens, league-page URLs or authentication data. No roster change, waiver claim, trade offer or draft pick is sent to Yahoo. Private local raw snapshots used in development stay outside the repository.

## Calculation contract

**Workload:** latest completed prior-week game compared with the preceding four available games. Current-week/future games are excluded. Older-season samples are labeled. Opportunity points are targets times position-wide receiving production per target plus carries times rushing production per carry, converted with the actual league scoring rules. This subtotal excludes passing, turnovers and returns; actual points include all supported scoring categories. Targets/snaps do not imply routes run.

**Baseline:** recency-weighted mean of up to eight earlier league-scored games, weights `0.85^i` from most recent backward. Where history is missing, an available current Yahoo forecast is a labeled carry-forward baseline. No missing forecast becomes zero. The report card uses rolling-origin held-out games with at least four earlier observations; it validates only the historical baseline, not matchup adjustments, Yahoo fallback or playoff probabilities. The first live run evaluated 4,990 held-out player/team games across QB/RB/WR/TE/K/DEF.

**Matchups:** positional player-game points allowed minus each player's average in other available prior games, shrunk toward zero with 20 neutral player-game equivalents. Prior-season evidence is included. This descriptive adjustment does not alter Yahoo or Qwen columns. It is used only in the lab's future-week lineup estimates. K/DEF adjustments are withheld when the comparable data is absent.

**Lineups:** slot-mask matching assigns each eligible player once, preserving current game locks and completed starter actual scores. Pins/exclusions use the existing exact source-specific optimizer. Missing data prevents complete estimates; structural empty slots can score zero only in the explicitly labeled no-pickup playoff scenario.

**Trades:** compare best legal starting lineups during the next three weeks after the current week. Current injury exclusions carry forward; recovery is not assumed. Unequal trades remove the lowest-baseline eligible unlocked bench player when a team would exceed its current roster count. Reserve-slot players and players whose game is locked cannot be traded in this model. A freed slot stays empty; no successful pickup is assumed. The interface identifies required drops/open slots and tells the user to verify Yahoo cut restrictions. Automated scanning considers each team's top eight unlocked baseline players and returns up to twelve qualifying one-for-one proposals; it is a bounded search, not exhaustive. Manual selection supports larger trades. These are lineup scenarios, not calibrated trade values or acceptance probabilities.

**Waivers:** compare next-week legal lineups after replacing each unlocked bench option. Opponent demand means adding the candidate would improve that roster's next-week starting total by more than one point; that demand calculation assumes the opponent can create a slot. It is not a prediction of their action. Priorities come from imported standings. No FAAB or winning-bid probability is fabricated.

**Playoff scenarios:** 1,000 deterministic-seed simulations use the reciprocal remaining head-to-head schedule, imported records, NFL byes, eligible lineups and historical scoring variability. Median-enabled leagues receive both weekly results. Already recorded weeks are not counted again; inconsistent partial standings withhold simulation. Win-equivalents (ties count one-half) then points for determine qualification. Unsupported divisions or missing mandatory inputs withhold results. Empty starting slots are counted visibly and score zero under the no-future-pickups assumption. Completed current-week starters contribute fixed actual points and no remaining variance. Future scores are modeled independently, with position variability priors for thin histories. The UI explicitly labels probabilities experimental and uncalibrated. It does not forecast future trades, injuries, recovery, pickup success or playoff-game championships.

## Validation and release

Unit tests cover exact source pins, duplicate-player prevention, locked starters/bench players, missing projections, byes, two-for-one drops, locked trade rejection, reciprocal schedules, median wins, explicit empty-slot scenarios and exclusion of future observations. Browser checks cover sortable workload tables, shared player/compare modals, trade proposal selection, claim changes, draft mark/undo controls and 390px mobile containment. Production verification and final release test count are recorded in Release Notes.

The additive table can remain after rolling back the web image. Restore the source archive and recreate only the fantasy web service; never remove the PostgreSQL volume. Restore the prior importer script to remove additional scouting reads if needed.
