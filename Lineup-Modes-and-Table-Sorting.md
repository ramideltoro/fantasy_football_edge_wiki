# Four lineup playbooks and sortable tables

On **My Team → Lineup & decisions → Put your heavy hitters in**, four toggles immediately recalculate the eligible lineup, suggested changes and projected improvement. The default is **Yahoo only**.

- **Yahoo only:** original imported Yahoo projections (`providerProjected`), including explicit missing values. It never substitutes Qwen.
- **Qwen only:** current Qwen forecasts. Stale or absent forecasts do not fall back to Yahoo.
- **Bookies only:** the existing active VegasInsider-derived points. Offensive players have partial prop subtotals; K and DEF have the separately labelled game-line models. No Yahoo/Qwen points are substituted. Because prop coverage differs, this is a market-score lineup, not a complete team fantasy forecast; missing categories can skew FLEX rankings. The source limitation remains visible beside the toggle and improvement.
- **All combined:** equal-weight mean of available Yahoo, Qwen and a book-informed full estimate. For K/DEF the existing full specialist model enters directly. For offensive players, partial book points are completed with the league-scored historical baseline's **unquoted** categories: `book-informed = book subtotal + sum(uncovered baseline components)`. Covered passing/rushing/receiving yards are replaced once; anytime TD replaces rushing and receiving TD components only. Passing TDs, receptions and penalties remain in the historical remainder when not quoted. This modeled completion is explicitly disclosed; it is not a full sportsbook-published prediction and has not been demonstrated to outperform individual sources. If the current baseline cannot map all covered markets, that partial source is omitted rather than averaged directly with full forecasts.

The expandable **Open the playbook** table shows every roster player's raw Yahoo/Qwen/bookies values, completed book-informed estimate and its historical remainder, selected score and availability. Combined lineup cards show the number of usable sources out of three. Missing sources are omitted from the mean, never treated as zero.

All modes share the same position/FLEX eligibility, bye, unavailable-status, reserve-slot and game-lock constraints. Locked starters stay in their existing slots. Missing source coverage is named; a source that cannot fill all open slots does not invent a complete lineup. Missing current-starter forecasts do not produce a fabricated improvement. Equal projected totals prefer the assignment with the fewest changes, including a floating-point tolerance so equivalent FLEX assignments do not create zero-gain shuffles. These are recommendations only; no Yahoo transactions are performed.

## Table interactions

Every application table uses `src/SortableTable.tsx`, including roster, waivers, standings, player history, Qwen calculations, sportsbook calculations and raw receipts, candidate comparisons, team analysis and operations. Click any column heading to alternate ascending/descending. Headers are keyboard buttons and announce the current direction with `aria-sort`.

Numbers, percentages, signed scores and dates sort by value; names/text use natural case-insensitive ordering. Standings records sort by winning percentage, counting a tie as half a win. Raw odds sort by the quoted line when present, otherwise American price. The specialist implied team/opponent column sorts by the first (team) number. Missing values remain last in either direction; ties preserve the prior source order.

Roster and waiver sorting happens across the full filtered list **before** the 30-row page is selected. Changing sort resets to page one. Position filters and periodic dashboard refreshes preserve the selected sort. Comparison checkboxes stay attached to player IDs when rows move; Compare itself is sortable by selection.

Qwen numbers and start percentages now act as underlined explanation buttons without the repeated “Why this number?” caption. Missing/locked coverage messages remain visible. The modal still includes role/health evidence, league scoring, source links and timestamps.

## Verification and rollback

77 tests pass, including source isolation, three-source blending without double-counting, stale/missing coverage, signed values, kickoff and eligibility constraints, and tied-lineup stability. The existing news-evidence fixture now freezes its test clock to its publication date instead of aging into a failure after 48 hours. TypeScript and Vite builds pass.

Local browser verification used the built application with live public API data: all ten waiver columns change sort direction, numeric percentage order is correct, sorting from page two resets to page one, checked players survive reordering, and the percentage opens its explanation. All four lineup modes render their own scores and changes; tied combined scores retain the current slots. A 390 px mobile viewport has no document overflow and browser error logs were empty.

Deployment backup: `/var/backups/fantasy-football-edge/lineup-sorting-20260918/` on the authorized backend VPS. It contains the previous source, database dump and build/test logs. No schema migration or odds-collection schedule change is part of this release. Restore the source and rebuild/recreate the web service to roll back application behavior; preserve current database contents.

Production verification: the deployed asset matched the build; `/healthz` returned `ok`. All four live playbooks recalculated the current roster (Yahoo +0.07, Qwen +3.19, bookies partial-score +1.86 and combined +0.00 at verification time). Locked Gibbs/Moore stayed fixed. All eight combined-input table columns responded to sorting, and league points-for sorted numerically. The existing eight-book, 4,907-player-quote/900-game-quote snapshot and next six-hour due time survived the deployment. These example gains change with subsequent imports.
