# Navigation and weekly overview

September 19, 2026: organize tools by the decision they support, with a high-level Overview. The amber palette, Apple system font stack and player dossiers remain.

## Overview

Only three primary blocks remain:

1. **Who’s ready to rumble?** stays at the top. The healthy, injury-watch and Qwen likely-out counts open the corresponding player dossiers. Existing overlap and missing-evidence labels are preserved.
2. **This week’s showdown** shows your team and opponent, each team's actual score and Yahoo live projection, plus a short projected-margin summary. Missing values stay unknown. Live projections are clearly distinguished from scored points.
3. **The locker-room read** retains the weekly commentary and links to Set lineup and Kickoff watch.

The health summary spans the page. On large screens, the matchup and commentary sit beside each other; narrow screens stack them. The change feed, scouting shortcuts, individual alert lists, tracking metrics and detailed charts no longer occupy Overview.

## Where tools live

| Section | Group | Tools |
| --- | --- | --- |
| My Team | This week | Roster, Set lineup, Kickoff watch |
| My Team | Plan ahead | Three-week plan, Matchup radar |
| Waivers | Find players | Available players, Breakout radar |
| Waivers | Plan a pickup | Pickup impact, Claim coach |
| League | Around the league | Standings, Trade finder, Playoff race |
| Research | News & analysis | News & advice, What changed, Team analysis |
| Research | Past results | Projection accuracy, Weekly recap |
| Research | Preseason | Draft Room |
| Operations | Data & refresh | Worker activity, Import health |

**Roster → Weekly scoring breakdown** starts closed and contains “Who’s carrying the cooler?” (Yahoo forecast and actual player scores) and original-versus-live Yahoo matchup projections. Charts render when the disclosure opens.

**Available players → Waiver shortlist** starts closed and retains up to three QBs followed by other-position candidates. The full waiver table follows it. **Kickoff watch → All roster injury & bye flags** preserves the former Overview list, including bench flags, in a closed disclosure below the kickoff tools.

Every page keeps the import timestamp, overdue warning and a compact **Sources & refresh** link. The full source/refresh panel now lives in Operations. Source-specific projection explanations and freshness labels remain with the player tools.

## Compatibility and boundaries

`src/navigation.ts` centralizes page labels and group membership. Existing hash destinations—including `#Recommendations`, `#AI%20insights`, `#Report%20card` and `#Draft%20Room`—remain valid, along with browser Back/Forward and kickoff-notification links. Visible labels can change without breaking saved URLs.

No projection calculations, source collectors, refresh schedules, API authorization or Yahoo write behavior changed. This release does not write lineups, claims or trades to Yahoo. Manager identities remain protected by the existing public response contract.

## Validation and deployment

- Production TypeScript/Vite build succeeds; all 113 existing tests pass.
- Browser checks at desktop and 390 px phone widths cover grouped menus, health dossier opening, four lineup source choices, relocated/collapsible charts and shortlist, actual chart labels, existing Team analysis bookmark and Draft Room navigation.
- The moved chart retains the observed positive and negative actual-score labels; unknown actuals remain blank.
- Source and database rollback copies are stored in the restricted VPS directory `/var/backups/fantasy-football-edge/navigation-20260919/`, with the previous container image recorded. No database migration was needed.
