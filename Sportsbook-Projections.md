# Sportsbook projections — VegasInsider

The roster and waiver tables now have a **Bookies projected** column. Clicking the number opens a popup with the component calculations, every collected player quote, exclusions, matchup odds, source links, collection time and next scheduled refresh. Player dossiers and comparisons include the same information.

## Coverage and meaning

The collector reads the public [NFL player-prop board](https://www.vegasinsider.com/nfl/odds/player-props/) and [NFL game-odds board](https://www.vegasinsider.com/nfl/odds/las-vegas/). Safari access was checked, but the displayed boards also load without a login, so the backend does not store Safari cookies or depend on the Mac staying online.

Initial coverage includes Bet365, BetMGM, BetRivers (labelled RiversCasino by the source), Caesars, DraftKings, FanDuel, Fanatics and Hard Rock. PrizePicks and Sleeper are collected and displayed as pick’em operators, separately from the sportsbook average. Open and Consensus are reference columns, not additional books.

Available player markets are passing yards, rushing yards, receiving yards and anytime touchdown odds. Game markets are full-game spread, total and moneyline. The collector reads all players and all populated book columns, including rows initially hidden behind the source’s “See All” control. It does not follow affiliate/betting links, place bets, or scrape unrelated sports and casino content.

**Offensive player-prop numbers are explicitly partial subtotals.** The board lacks receptions, passing touchdowns, interceptions, fumbles and conversions. Missing components are unknown, never silently treated as a complete zero-valued forecast. The subtotal must not be compared directly with complete Yahoo/Qwen projections. Yahoo, Qwen, lineup optimization and existing ESPN market context remain separate. K and DEF use separately labelled game-line models, described below.

## Calculation

1. Match the source player name to a unique roster/pool identity, normalizing punctuation and suffixes. Ambiguous matches do not receive a number.
2. Require the same season/week on the game board and roster, one matching team event, and agreement with the Yahoo kickoff when supplied. Normalize JAC/JAX, WAS/WSH and LA/LAR team codes.
3. Require at least three eligible sportsbooks **per market**. Pick’em quotes are visible but excluded. All qualifying books receive equal weight. Coverage may differ by market.
4. Use yardage lines as yardage proxies, not statistical means. Multiply their arithmetic average by the league’s Yahoo scoring value. Do not assume default scoring when a rule is missing.
5. Convert an anytime-TD American price `a` into probability: `100/(a+100)` when positive; `|a|/(|a|+100)` when negative. Estimate non-passing TD count as `-ln(1-p)`, a Poisson-model assumption. Average the per-book counts, then multiply by the league’s TD points. The combined TD market is usable only when rushing and receiving TD scoring are equal. Passing TDs are never inferred from this market. A one-sided price retains bookmaker margin; this is not a no-vig forecast.
6. Flag (but retain) TD probabilities more than 25 percentage points from the sportsbook median. Flag yardage lines farther than `max(15 yards, 50% of |median|)` from the median. These filters require at least three comparable books. The source initially showed apparent opposite-side prices in some Hard Rock TD cells; raw values and exclusion reasons remain inspectable.
7. Sum the unrounded component contributions and round the displayed subtotal to two decimal places.

VegasInsider’s combined player board does **not** label each prop with an event ID or an individual quote timestamp. Association with the current game board is an explicit inference, disclosed in the popup. Collection time is not represented as a sportsbook’s last price-change time. This source limitation cannot establish that every individual prop has rolled over to a new game.

Numbers are withheld when the player is listed out/IR/PUP/suspended, the game is locked/completed, the kickoff has passed, the source is more than six hours plus five minutes old, season/week or kickoff disagree, the player/game match is ambiguous, the position lacks usable scoring markets, or insufficient comparable books remain. Quotes and reference calculations remain inspectable with an inactive/old-data warning.

## Kicker and defense models — September 18 follow-up

K and DEF do not have sufficient direct player props on this board. Their **Modeled** numbers combine spread/total pairs from at least three sportsbooks with league-scored nflverse team history. The popup includes each book's lines, implied team/opponent points, per-book fantasy estimate, consensus calculation and all raw game quotes. Moneylines are context; reference columns are not counted as books.

- **K:** `(game total − team spread) / 2 × historical fantasy kicking points per team NFL point`. The historical kicking share uses distance-specific made field goals and extra points, recency weighting and shrinkage toward NFL team history. It assumes the player is the team's primary kicker.
- **DEF:** historical league-scored sacks, turnovers, blocks and touchdowns, plus expected points-allowed points. The opponent's implied NFL total is `(game total + team spread) / 2`. A normal approximation with an explicitly assumed 10-point standard deviation distributes that total across the league's scoring brackets instead of selecting a single midpoint bracket.

Each qualifying book receives equal weight. These are our modeled estimates derived from betting lines, not sportsbook-published player fantasy projections. They are not live-game estimates and are not proven superior to Yahoo. The same week, kickoff, freshness, availability and game-lock checks apply. Completed players wait for a matching upcoming-week snapshot/market; current-game or next-week odds are never silently mixed. [League scoring and explanations](League-Scoring-and-Explanations.md) covers baseline construction and historical points-allowed limitations.

## Refresh and persistence

`server/sportsbookService.ts` creates `sportsbook_state` (singleton current board, attempted time, next due time, error and failure count) and `sportsbook_history` (complete successful boards retained for 30 days).

The backend checks once per minute, collecting both pages when due. Successful collection schedules the next pull six hours later. Restarting the container preserves the due time and last successful board. A PostgreSQL advisory lock prevents overlapping collectors. Both pages must parse successfully before the board is atomically replaced. Failure preserves the last successful board and schedules a retry in 15 minutes, backing off to one hour after repeated failures. Request timeouts, a 12 MB page-size limit, structural validation and duplicate-key checks bound failures.

- `GET /api/sportsbook`: source health, last/next refresh, book/operator lists and quote counts.
- `GET /api/sportsbook/board`: complete normalized quote snapshot, source status and timing.
- `POST /api/sportsbook/refresh`: owner-session or existing import-token authorization. Empty JSON checks whether refresh is due; `{ "force": true }` explicitly refreshes. No new credential is needed.
- `GET /api/dashboard`: enriches each roster/waiver player with the calculated partial or specialist modeled projection and supporting quotes.

A Codex heartbeat (`refresh-fantasy-sportsbook-odds`) checks health every six hours and requests a due refresh only when needed. Routine collection runs on the VPS regardless of whether the desktop app is open.

The portal polls its dashboard every ten seconds and the market health strip every minute. No bet placement or account modification is involved.

## Validation and rollback

Source-derived HTML fixtures cover book/column alignment, all available operators, game identity, blank cells and American prices. Regression tests cover per-market averaging, league scoring, minimum book counts, pick’em exclusion, anomalous TD prices, stale data, game locks, ambiguous identities, week/season and kickoff mismatches, and unsupported positions.

The pre-release source/database backup is `/var/backups/fantasy-football-edge/sportsbook-20260918/` on the backend VPS. Existing credentials, Google/Yahoo access and database volumes were preserved. To revert application behavior, restore that source archive and rebuild/recreate the web service; the additional sportsbook tables can remain without affecting the old application. Do not overwrite the earlier game-day backup.

Release validation: 61 tests passed locally and in the production container; TypeScript and Vite builds passed. The first live board collected at 2026-09-18 05:04:57 UTC contained 4,788 player quotes for 480 players and 900 game quotes across 15 games with posted lines. Its next normal collection was scheduled for 11:04:57 UTC.

Live browser verification covered the roster number popup, waiver column and two-player comparison, plus a 390 px mobile viewport. The refresh endpoint rejected an unauthenticated request with HTTP 403. Recreating the container retained the exact successful snapshot and six-hour due time without a duplicate collection.
