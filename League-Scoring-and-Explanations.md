# League scoring, explanations and the weekly overview

September 18, 2026 follow-up at https://fantasy.ramideltoro.com.

## What changed

- Waivers replaces **Fantasy position** with **Qwen probability start**. The roster retains its fantasy assignment column. Both Qwen points and starting probability open a keyboard-accessible explanation dialog; the historical **Started** percentage remains a separate NFL starts/appearances metric.
- **Who's ready to rumble?** moves to the top of Overview. A weekly matchup commentary follows, using the imported opponent, Yahoo live projected finish, actual team scores, remaining high-projection starters and availability flags. Player mentions open their dossiers. It is generated from those facts and capped at 200 words; it is not a claimed Qwen narrative or a win probability.
- **Make the next move count.** and its decision queue move to Research.
- **Who's carrying the cooler?** uses the same amber `#ffbb38` and mint `#83d5af` for its projected/actual bars and square legend symbols. Actual labels retain negative scores; games without results remain unknown. Yahoo scored results are authoritative. If a locked player's total is missing, imported scoring statistics can supply a partial calculated result until Yahoo posts its total.
- League display aliases are exactly `ashok Legit Team` and `Sekou Superb Team`. These are portal display changes; no Yahoo team/account names were edited.
- Headlines and body copy use Apple system typography: `-apple-system`, `BlinkMacSystemFont`, SF Pro Display/Text, Helvetica Neue and Arial fallbacks. SF renders on supported Apple devices. No Apple font files are redistributed; the bundled Anton/Inter dependencies were removed. Amber/charcoal styling and reduced-motion behavior remain.

## Correcting the Qwen points

The active method is **qwen-points-v5**. The previous free numerical output could confuse an NFL game total with an individual fantasy forecast: Jason Sanders showed 44.50. The new model receives explicitly league-scored choices and must select a valid value. Legacy v4 forecasts cannot populate the new column.

`shared/leagueScoring.ts` scores expected statistics using the imported league settings. Kicker scoring covers made field goals at 0–19, 20–29, 30–39, 40–49 and 50+ yards, plus extra points. Defense covers sacks, interceptions, fumble recoveries, defensive scores, safeties, blocked kicks, return scores and every points-allowed bracket. Offense includes PPR, yards, touchdowns, turnovers and conversions. Missing required coefficients produce no baseline rather than assuming default scoring.

The baseline uses up to eight prior regular-season games (current and previous season), newest first with weights 1, 0.8, 0.64 and so on. Three positional peer-game equivalents shrink small samples toward a broader historical mean. Offensive peers have minimum usage thresholds. Rookies/missing individual history use the positional prior. Backup QBs receive an explicitly disclosed 10% baseline workload assumption until starter evidence changes. Future/current-week outcomes never enter the pregame baseline.

Qwen receives the baseline, recent league-scored games, role, availability, matched reporting and available ESPN game context. Yahoo projected points are excluded. It chooses the baseline or a ±5/10/15% adjustment; a two-point minimum adjustment scale handles near-zero/negative defense baselines. Explicit absences/bye override to zero. New forecasts after kickoff are null. This bound prevents unsupported 44.50-style outputs; it does not demonstrate forecast accuracy.

Six-player jobs require exact player IDs, valid candidate values, whole-number probabilities and start probability no higher than play probability. Explanations are assembled from grounded input facts and model-selected evidence keys. The dialog shows each expected scoring quantity × league multiplier, baseline, adjustment, final result, recent samples, peer weights, source links and generation time. Starting probabilities are subjective estimates of the upcoming role, not empirically calibrated probabilities or the historical NFL start percentage. DEF units have no individual start probability.

Jobs and results remain persisted and deduplicated. Roster and specialist work is prioritized, stale values stop driving lineup advice after four hours, and invalid responses are rejected. The unchanged Mac worker performs real Qwen inference. Research version 11 triggers the new baselines; old method records remain for audit, not active display.

Defense history uses opponent final game scores for points-allowed buckets. Yahoo can exclude some defensive return scores, so these are disclosed historical estimates rather than claims of exact official Yahoo past defense totals.

## Specialist sportsbook estimates

See [Sportsbook projections](Sportsbook-Projections.md). K and DEF now use explicitly labelled **Modeled** forecasts from matching next-game spread/total pairs and league-scored team history. They are our estimates derived from the market, not player fantasy points published by a sportsbook. At least three books must contribute; the current source provides eight. Completed games, stale odds and wrong-week markets remain unavailable rather than being presented as fresh next-game predictions.

## Validation and rollback

The release has 67 passing tests and a successful TypeScript/Vite production build, including custom PPR, negative actuals, field-goal distance scoring, defense brackets, constrained Qwen output, minimum sportsbook coverage and stale/locked data regression checks. Production tests run with the tests directory mounted read-only because it is excluded from the runtime image.

Live checks confirmed Jason Sanders at **10.24 Qwen points** and **8.90 modeled sportsbook points**, with a working 95% starting-probability explanation. Other live examples include Broncos Qwen 7.27 and sportsbook 6.87. These are time-specific forecasts, not fixed outputs or accuracy claims. Chart checks confirmed Jahmyr Gibbs 23.30 and DJ Moore −0.10, with exact bar/legend color matches. Research placement and both renamed teams were verified in the live browser.

The source and PostgreSQL backup before this follow-up are retained at `/var/backups/fantasy-football-edge/calibration-20260918/` on the backend VPS. Restore its source archive and rebuild/recreate the dedicated web service to revert application behavior. Existing credentials, database volumes, six-hour odds scheduler and Yahoo importer cadence were preserved.
