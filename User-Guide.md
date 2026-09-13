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
