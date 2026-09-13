# AI team summary

The AI insights page now includes a whole-team briefing for the current imported week: roster size, active-slot injury/bye flags, Yahoo matchup projections when parsable, eligible lineup gain and assignments, and waiver scope. Qwen ranks the evidence-backed priorities; the server renders canonical fact text rather than accepting unsupported generated claims. Individual player actions remain below the briefing.

The briefing refreshes with each imported snapshot using the existing separate Mac Qwen worker. Existing latest research is rebuilt once for version 2. Capture and generation timestamps are visible. Unknown matchup data is explicitly unavailable. Projected point gains are not win-probability gains. Claims require manual review for current locks, claim cost, timing and roster space. The import remains limited to two W/R/T pages; no additional Yahoo traffic is introduced.

Validation: automated grounding tests reject invented priority categories and deduplicate priorities; application build and existing importer/forecast tests pass.
