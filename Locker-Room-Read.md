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

## Narrative reaction GIFs and the coach’s voice

The September 19 personality update adds one compact reaction GIF inside the existing read. It follows the structured assessment, not keyword matching or random decoration. Source collection and the 30-minute editorial refresh continue unchanged; a stable GIF URL avoids restarting the animation on every dashboard poll.

| Evidence | Reaction |
| --- | --- |
| Stale source, waiting matchup, missing/nonfinite margin, or unverified final result | Puzzled coach; get fresh film |
| Pregame/live projected margin below −5 | Rocky training; underdog comeback work |
| Pregame/live margin from −5 through +5 | Sweating reaction; every decimal matters |
| Pregame/live margin above +5 | Coach applauding; finish the job |
| Explicit current-week final win | Touchdown dance |
| Explicit current-week final loss | Disappointed sideline; regroup |
| Explicit current-week final tie | Puzzled coach; dead heat |

Stale inputs override celebrations. Final reactions use imported W/L/T results, never projected margins. `shared/coachMood.ts` is the decision contract, `src/coachReactions.ts` is the curated catalog, and `CoachReaction.tsx` handles display. Seven states use six attributed GIPHY clips, primarily from the official NFL and Rocky channels. These are editorial reactions, not footage of this fantasy matchup. Source pages are linked below each GIF.

GIF media is loaded directly from `https://media.giphy.com`, the only new image host allowed by CSP. No third-party iframe, script, API key, backend scraping, media storage or paid service is introduced. A no-referrer policy applies to images and external attribution links. Pause switches to a static frame and persists in browser local storage. The operating system’s reduced-motion setting uses the still image and responds to preference changes. If media fails, the caption and full assessment remain readable with a compact fallback.

Editorial copy across Overview, My Team, Waivers, League, Research and Operations now uses the same direct, funny, optimistic, occasionally profane coach’s voice. Navigation names, numerical labels, statuses, source quotes, scoring calculations and safety/lock explanations retain their precise meaning. The existing amber palette, Apple system font stack, player dossiers, sortable tables and collapsed tools are retained.

`shared/coachVoice.ts` supplies one writing contract to Qwen’s team/waiver and news requests. The worker system instruction agrees with that voice while preserving exact quotes and calculation rules. Team-brief factual templates use the same language immediately; previously generated Qwen prose changes through normal analysis refreshes. The installed Mac worker receives the changed prompt modules as well as the deployed application. No projection cache is invalidated just to change writing style.

Validation: TypeScript/Vite build and all 127 tests pass. Reaction tests cover boundary margins, explicit final results, stale/missing data, and static/animated media pairs. Browser checks cover remote image loading, pause/resume, persisted preference, reduced motion, provider-failure fallback, runtime errors, and all 20 secondary pages at 390 px. Desktop and mobile screenshots were inspected. Production verification and rollback are recorded in the release notes.

Production confirmation: `/healthz` returned `ok`; the live dashboard returned a fresh `underdog` reaction for a −20.02 Yahoo projected margin. The deployed CSP allowed the GIF, and the production browser checks passed for image loading, pause/resume, reduced motion, fallback and all mobile routes without runtime errors. All 127 tests also passed against the built container. When running the full test suite against the runtime image, mount both `tests` and `src` read-only: the production image intentionally carries compiled frontend assets, while the reaction-catalog test imports the original source module. Application commit: `7465011`.
