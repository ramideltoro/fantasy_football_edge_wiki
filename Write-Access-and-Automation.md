# Advice and automation

V2 automates reading, validation, storage, headline refresh and recalculation. It does not write Yahoo lineups, submit waivers, execute trades or post messages.

The initial optimizer maximizes Yahoo imported current-week projected points across eligible unlocked roster slots. Injury/bye exclusions and kickoff locks are hard constraints. An incomplete eligible roster produces an explicit unavailable result. A missing current projection does not create a fabricated improvement.

Potential enhancements should be evaluated using prospective stored forecasts: independent source blending, uncertainty intervals, replacement-value waiver rankings and longer-horizon trade analysis. News volume alone is not evidence that a player will score more points. Source availability and permitted reuse must be checked before adding a provider.

If Yahoo write authorization becomes available later, implement a separate action adapter with explicit action review, current roster/version checks and an audit log. A read connector must never imply write permission.

The implementation includes an exploratory calibration panel. It remains unavailable until 30 prospectively scored player forecasts exist, then reports mean-error-adjusted projections and empirical 10th–90th percentile error ranges pooled across positions. This is an initial calibration method, not a validated independent model. Server receipt time prevents a late upload with a past kickoff from counting as a prospective forecast.
