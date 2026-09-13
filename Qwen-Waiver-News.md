# Qwen waiver briefing

The Waiver list now starts with a ranked Qwen shortlist (up to five suggestions), canonical statistical/depth rationale and the reporting Qwen selected. A separate VPS research adapter retrieves ESPN, Yahoo Sports, CBS Sports and FantasyPros RSS feeds, plus Google News RSS searches for the top twelve eligible imported players. Feed excerpts are provided as untrusted evidence to the existing private Qwen server through the Mac worker. No article paywalls are bypassed and no new Yahoo browser pages are requested.

Sources are cached in research_cache for one hour. Failed fetches retain cached data with a stale label; only dated items within seven days (and at most one hour future clock tolerance) are passed on. Matching uses normalized full names in titles/excerpts; Google News results are explicitly labeled player-search results whose relevance requires review. These searches can retrieve broader articles, not confirmed player-specific reporting. Source publication and retrieval times are shown. This is not comprehensive coverage or an outlet consensus.

Qwen considers the top twelve projected eligible waiver players plus news-matched candidates, at most twenty from the existing fifty-player W/R/T import. It ranks up to five and selects evidence and article indices. The server rejects unavailable/owned players, unknown fact keys and nonexistent citations. Model projections are unchanged; Qwen does not invent numerical forecasts. Current browser-side locks and changed availability are flagged. No roster transactions occur.

Research version 4 rebuilds the latest intelligence record on rollout; future snapshots use the same automatic research/worker flow. The schema-constrained response adds waivers alongside the existing team priorities and player insights. Worker uses a bounded 8K context and 1,600 output tokens.

Validation: 23 automated tests pass, including invalid waiver/citation rejection and normalized name matching; production build passes. Four direct outlet feeds were verified live on rollout. Browser verification checks the briefing above the waiver table.

Inference optimization: citation URLs remain server-side and are omitted from Qwen input; per-article input excerpts are capped at 300 characters. The initial larger news request timed out, prompting this reduction.
