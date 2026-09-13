# User guide

- **Overview:** current starter totals, projected lineup opportunity, injury/bye watch and projected-versus-actual chart.
- **My roster:** searchable roster with slots, injury flags, kickoff locks, projections, actuals, roster/start percentages and player details.
- **Player lab:** imported player pool, popularity-versus-projection chart, up to three side-by-side player comparisons, per-player projection/popularity history, and a prospective forecast scorecard. Player tables are paginated in groups of 50.
- **Recommendations:** the highest-projected eligible unlocked lineup plus a shortlist of imported free agents/waivers. A trade/waiver scenario compares an outgoing and incoming player’s current-week points and flags locks, positions and availability. Changes remain advice; apply decisions yourself in Yahoo.
- **League:** anonymized public matchup and standings; Google owner sign-in reveals imported league page detail, rules, schedule, transactions and draft history.
- **News & trends:** attributed ESPN/Yahoo headlines linking to original articles. Player details show historical imported metrics when repeat samples exist.
- **Import health:** last capture time, freshness, history samples and owner-only import events.

A roster refresh is a snapshot, not a live feed. Check freshness before acting. A dash means unavailable, not zero. Yahoo projections and win probabilities are provider estimates. Current points, original projection and live projection have different meanings.

The optimizer respects roster slots, flex eligibility, byes, unavailable statuses and game locks. It does not incorporate an independent injury model, trade acceptance probability or a guarantee of winning. Compare player context and fresh news before executing a move.

## Refresh on demand and trade candidates

Sign in as the owner and click **Refresh from Yahoo** near the top of the portal. This queues a durable request for the Mac importer. The Mac checks every minute while awake and online, bypassing the ordinary hourly/football-window cadence for an explicit request. Existing Yahoo rate-limit cooldowns still apply. Repeated clicks coalesce into one pending request; a successful snapshot captured after the request completes it. The reload icon only reloads the dashboard.

Under **Recommendations → All trade & waiver candidates**, browse the complete imported candidate pool in pages of 50, search by name/position, or filter free agents/waivers versus rostered trade candidates. Columns include position, NFL team, availability, NFL starter/backup role, and projected points. Select a row to populate the incoming player comparison. Rostered candidates are not necessarily offered on Yahoo's trading block; their managers must agree to a trade.

NFL roles use ESPN depth-chart order, matched by NFL team and normalized full name. First at a listed offensive position is Starter; subsequent entries are Backup with depth rank. Multiple WR starters are valid. Team defenses are labeled separately, and unmatched players are Unknown. Roles link to the source depth chart; role is not a promise of playing time or current injury clearance. Depth data is fetched independently of Yahoo and cached for one hour.

## Dedicated waiver list

Open **Waiver list** in the main navigation. Its first column is real **NFL starter / backup** status, followed by player, position, NFL team, availability, injury status, bye week, projected points, and rostered percentage. Click a player name for details and history. The list includes free agents and players on waivers, excludes rostered trade candidates, supports name/position/team search and availability filters, and paginates 50 rows ordered by projection. Missing depth-chart matches remain Unknown.

**My roster** also shows NFL starter/backup as its first column, linked to ESPN depth charts. This is the player's real NFL depth role, separate from your fantasy Slot and Yahoo Started percentage. Missing matches show Unknown; defenses show Team defense.

## Yahoo refresh operations

The **Yahoo refresh** navigation section is owner-only and combines the on-demand refresh button with request state, worker last check-in, reported state, cooldown deadline, and the latest 150 timestamped operation logs. Logs refresh every five seconds while the section is open. New imports report each roster/page group and player page, upload, and success/failure. An old check-in does not establish that the Mac is online now. Yahoo cooldowns remain enforced and queued requests survive failures.

The waiver list is now intentionally limited to the first two Yahoo pages of available W/R/T players sorted by projected points (up to 50). QB, kicker, defense and deeper waiver candidates are not imported into this shortlist. Your own full roster remains available separately.

## AI insights

Open **AI insights** for Qwen's weekly decision brief, projected-player rankings, suggested lineup, and accuracy comparison with Yahoo. Click a player name to chart recent league-scored points, targets, carries and snap percentage, and view matching headlines. Source timestamps and missing data appear with the analysis. Early-season forecasts intentionally match Yahoo until enough current-season games exist. Starter status alone is not a prediction of performance. Qwen comments are explanations to review, never automatic roster transactions.
