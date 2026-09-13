# Yahoo setup and import coverage

## Browser session

The importer uses a dedicated Chrome profile under `~/Library/Application Support/FantasyFootballEdge/browser`. Cookies remain local. The VPS does not receive the Yahoo password or cookies. To authenticate, run `npm run login:yahoo`, sign in and close that dedicated browser. Where locally supplied credentials are available, a one-time local login helper may enter them without storing them in the repository. MFA, CAPTCHA or account verification requires the owner.

Private `config.json` example (replace placeholders locally):

```json
{
  "endpoint": "https://fantasy.ramideltoro.com",
  "token": "RANDOM_IMPORT_TOKEN",
  "rosterUrl": "https://football.fantasysports.yahoo.com/f1/LEAGUE_ID/TEAM_ID",
  "pages": [
    {"kind":"league","url":"https://football.fantasysports.yahoo.com/f1/LEAGUE_ID"},
    {"kind":"matchups","url":"https://football.fantasysports.yahoo.com/f1/LEAGUE_ID/matchup"},
    {"kind":"players","url":"https://football.fantasysports.yahoo.com/f1/LEAGUE_ID/players"},
    {"kind":"settings","url":"https://football.fantasysports.yahoo.com/f1/LEAGUE_ID/settings"},
    {"kind":"transactions","url":"https://football.fantasysports.yahoo.com/f1/LEAGUE_ID/transactions"},
    {"kind":"schedule","url":"https://football.fantasysports.yahoo.com/f1/LEAGUE_ID?module=standings&lhst=sched#lhstsched"},
    {"kind":"draft","url":"https://football.fantasysports.yahoo.com/f1/LEAGUE_ID/draftresults"},
    {"kind":"research","url":"https://football.fantasysports.yahoo.com/f1/LEAGUE_ID/research"}
  ]
}
```

Set directory permissions to 700 and configuration to 600. `EDGE_IMPORT_HOME` supports another private location. `EDGE_IMPORT_ENDPOINT` is a development override for candidate testing through a loopback SSH tunnel.

## Coverage

The initial browser check retrieved a 17-player roster and verified scoring settings, matchup, standings, transaction history, schedule and draft results. The expanded importer paginates all offense, kickers and defense in the current-week projection view. A verified run imported 1,195 distinct players across 59 total pages. Each position group records page/row counts and a completion flag; pagination stops at 80 pages per group as a safety bound. Coverage is limited to those views and must not be described as every historical Yahoo field. Owner league pages preserve readable source text, while roster, matchup and standings have dedicated normalized views.

Unimported historical views, pagination beyond the safety bound, premium forecasts, videos, chat, account settings and write actions are not implied by a successful roster import. Broader player and historical coverage must be validated as it is added.

## API history

Yahoo OAuth success alone did not prove fantasy-data authorization. Prior probes of two applications returned authorization errors for league reads. Browser import does not claim those applications are approved. A future approved Yahoo API adapter can replace browser extraction without replacing the portal's canonical data model.
