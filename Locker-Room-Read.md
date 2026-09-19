# The locker-room read

September 19, 2026: the Overview briefing now assesses the matchup in an optimistic, dramatic, profane coach’s voice. It is a factual, rule-driven coaching assessment, not a Qwen-generated prediction or a calibrated win probability. No paid service or additional model request is introduced.

## What the coach assesses

- Yahoo live final projections, the projected margin and movement from the original projected margin.
- Actual points banked, kept explicitly separate from expected final totals.
- The opponent’s most recent completed head-to-head result and up to four completed games of recent scoring form: record, average, low and high. One-game samples are labeled as such.
- How the opponent’s current forecast compares with recent scoring, and how our own current projection compares with our recent results.
- The highest Yahoo-projected eligible, unlocked starters on each side. Opponent players come from the current matchup when it can be oriented by known roster IDs; a separately timestamped scouting roster is the fallback.
- Legal Yahoo-only lineup improvement, availability flags, locked games and bench injury concerns. A missing/incomplete lineup calculation is not reported as a zero-point opportunity.
- A conditional route to winning or a postgame assessment once a completed current-week result is explicitly imported.

The main card shows the scoreboard assessment, opponent film and coaching verdict. **Open the full scouting report** reveals our own recent form, playmakers, opposing threats, availability context and a sortable game-receipt table. Names open the existing player dossiers. Amber accents, status badges, a prominent headline and a highlighted coaching call provide style without adding another navigation destination.

## Source and calculation contract

`shared/opponentHistory.ts` reads the imported Yahoo team schedules. Only rows with an explicit Win/Loss/Tie result, valid scores and a consistent outcome qualify. Current and future weeks are excluded from recent form. The first result/score line is the head-to-head result; the second league-median line is not another game. Completed games are deduplicated by week and the latest four are selected. Missing scores remain missing; real zero and negative scores are preserved. Public output includes team names and scores, not manager identities, private league URLs or account IDs.

The Yahoo API adapter now preserves optional completed status and both teams’ points in normalized league schedules. Only `postevent` matchups count as completed. Existing snapshots without these optional fields remain valid, and the browser import contract continues to work.

The current browser matchup’s player/projection columns are oriented by matching known own-roster player IDs. Ambiguous orientation is withheld. This avoids relying on which side Yahoo happens to display first. The synthetic opponent roster retains selected slots and final/live game flags; known scouting kickoff times are applied when available.

`shared/lockerRoomRead.ts` produces the headline, phase, sections, numerical evidence and receipts. The projected margin is own live projection minus opponent live projection; movement is that margin minus the original margin. Recent average is an unweighted mean of up to four completed games. The legal lineup gain uses the existing Yahoo-only optimizer and game-lock rules. These judgments do not alter Yahoo, Qwen, sportsbook or combined projections.

## Refresh behavior

`GET /api/dashboard` recomputes the read from the latest enriched snapshot and availability evidence. The existing open-page dashboard polling updates the card about every 10 seconds when new inputs arrive; closing the browser does not prevent the next request from producing a current assessment. Existing Yahoo import and availability workers supply fresh data on their established schedules. This is not a claim of a real-time sports feed.

The coach’s phrasing rotates on 30-minute editions. Repeated requests with unchanged evidence in the same edition return the same revision. New scores, projections, availability or source timestamps update the revision. `updatedAt` identifies the edition/evidence refresh, while the card separately shows the Yahoo snapshot time and latest input-check time. Schedule and opponent-roster timestamps are exposed in the expanded report. Overdue Yahoo data produces an explicit older-data badge and a coaching call to verify the sources before acting.

Final-result imports switch to a postgame huddle with a Weekly recap action. There are no new database tables, scheduled assistant tasks, Yahoo writes, notifications or source-scraping jobs for this feature. The former `matchupCommentary` field remains as a compatibility fallback.

## Verification and rollback

All 123 tests pass, including completed-versus-live history, league-median exclusion, zero/negative scores, four-game windows, projection margins and movement, legal lineup gains, missing inputs, stale evidence, privacy, refresh stability, final outcomes, mirrored matchup columns and normalized Yahoo API results. TypeScript/Vite production build passes.

Browser checks cover desktop and 390 px phone layouts, the collapsed/expanded report, opponent playmaker text, sortable receipt headers and the existing player modal flow. A representative current-season read correctly distinguishes the opponent’s Week 1 score of 106.70 from its Week 2 live forecast of 133.31, a 26.61-point difference; this is evidence for a conditional judgment, not a promise of regression.

Rollback copies of source, database and previous image identity are retained under `/var/backups/fantasy-football-edge/coach-read-20260919/`. The optional normalized schedule fields require no migration.
