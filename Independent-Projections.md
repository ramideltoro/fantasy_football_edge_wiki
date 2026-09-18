# Independent Qwen forecasts and expanded position imports

**Current implementation:** [League scoring and explanations](League-Scoring-and-Explanations.md) describes `qwen-points-v5`, deployed September 18, 2026. Qwen selects bounded league-scored forecasts with clickable calculation receipts, including K and DEF. The sections below record earlier iterations; `statistics-v1` is no longer the active Qwen column.

Yahoo scope: retain two available W/R/T pages, add exactly one available page each for QB, K and DEF, all sorted by current-week projected points. All four coverage markers must validate before a snapshot is accepted. Sequential page delay and single ZIP upload are unchanged. Live import verified 50 W/R/T, 25 QB, 25 K and 19 DEF rows.

Research adds nflverse weekly team statistics to the existing player stats and snap counts. Historical raw kicking/defense/offensive fields, league scoring, opponent, role and recent reporting are provided to Qwen in eight-player batches. Yahoo projected points are excluded from those requests. News includes publisher RSS feeds, player-specific Google News searches and public r/fantasyfootball Atom RSS. Reddit is unverified opinion. This uses Google News search, not a general Google Search API. Unavailable feeds are recorded rather than bypassed.

projection_jobs persists hash-deduplicated input, status, result and generation time. Current snapshots supersede old pending work; QB/K/DEF jobs are prioritized. The existing Mac AI worker processes these jobs independently of Yahoo browsing. The server validates complete player IDs, finite bounded forecasts and coherent low/high ranges, and refuses numerical estimates without historical or substantive directly matched evidence. Ranges are illustrative, not calibrated intervals. This validation cannot prove that Qwen's predictions or reasoning are accurate.

Current player views share the same active projection through applyProjections: Qwen when available and same NFL team, otherwise explicitly labeled Yahoo fallback. Yahoo is preserved as providerProjected. Qwen estimates expire from active display after four hours. Player details show reason, range, timestamp and research links. The roster, waiver list, player lab, optimizer and AI comparison use active values; historical Yahoo charts and Yahoo league matchup totals retain explicit Yahoo labels. Position suggestions show three eligible imported QB/K/DEF options. No transaction is performed.

/api/projections exposes batch status and prospective accuracy: earliest estimate completed before known kickoff versus completed imported actual points. Errors are compared against the same imported Yahoo baseline. Missing actual outcomes remain unscored. No claim of superior accuracy is made.

Forecasts with insufficient evidence remain null, so rookies or missing-history players can retain Yahoo fallback. Exact league scoring, recent data and robust forecast evaluation matter more than the volume of commentary. This is an experimental Qwen forecaster, not a demonstrated best-in-class model.

Refresh deduplication includes a four-hour window so unchanged evidence can receive a new forecast without overwriting earlier prospective records. Source changes can trigger earlier refreshes. The initial rollout processes the full pool progressively in the background; it does not wait for all batches before making completed estimates visible.

Live output review found unsupported matchup prose in Qwen's reasoning (including an incorrect expansion of an NFL team abbreviation). Player-facing explanations therefore use canonical input coverage, NFL role and opponent identifiers rather than repeating unverified model prose. Numerical estimates remain experimental Qwen outputs; the source-coverage wording does not assert that more sources imply better accuracy.

## Statistical forecast rollout (September 12, 2026)

The active numerical method is now `statistics-v1`. It calculates league-scored points for up to six prior current-season games, weighting each older game by 0.8 relative to the next. At least three games and recognized offensive scoring rules are required. The displayed range is weighted historical standard deviation, not calibrated uncertainty. Bye/out designations produce zero; insufficient evidence and unverified K/DEF scoring retain the explicitly labeled Yahoo fallback. Prior-season games and future games do not satisfy the minimum sample requirement. No numerical adjustment is invented from headlines, Reddit, weather or opponent descriptions.

Qwen remains the news/roster commentary service. Numerical forecasts complete in the application without an inference request, reducing shared Ollama demand. No model installation, Ollama restart, shared configuration change, or NutsNews endpoint change is involved. Larger-model testing is deferred: the current service uses CPU inference, and additional RAM alone does not establish acceptable latency.

Forecast records are immutable per evidence/window and preserve the original snapshot for prospective accuracy. The active accuracy panel measures only statistics-v1 forecasts saved before kickoff against completed imported outcomes, with paired Yahoo MAE. Older Qwen forecasts remain stored but are excluded from this comparison. The existing `qwenMae` response field is retained for compatibility and now labeled Statistical error in the UI. Accuracy is unproven until outcomes accumulate.

Verification: 30 unit tests and production build pass. Live deployment must also confirm health, forecast method, and roster availability. Screen-lock/headless Yahoo collection is unchanged.
