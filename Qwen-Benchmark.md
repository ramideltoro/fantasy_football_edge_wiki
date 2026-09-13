# Qwen 3B versus 7B: local-server benchmark

September 13, 2026. Production model remains qwen2.5:3b. The downloaded qwen2.5:7b model was unloaded after testing; its files remain available for future tests. No shared service configuration was changed.

## Method

Same five synthetic prompts, temperature 0, seed 42, JSON output, 8,192-token context capacity, 220-token output cap, two inference threads, sequential requests on the existing Ollama server. Production traffic continued. Each prompt was run once per model. Prompts cover arithmetic, legal lineup choice, news uncertainty, available waiver choice, and abstaining when evidence is absent. This is a small operational screening test, not a comprehensive model evaluation or real-world forecast-accuracy study. Actual input lengths were 70–120 tokens; full portal prompts and long-context retrieval were not benchmarked. No throughput/concurrency stress test was performed.

Hardware: Ryzen 7 5800H, 8 physical cores/16 threads, about 60 GB usable RAM, CPU inference. Sampled available memory stayed above 52 GiB. No swap pressure was observed. Existing CPU work used about 50% of logical CPU capacity during spot checks. CPU use is not isolated per request.

## Results

| Measure | 3B | 7B |
|---|---:|---:|
| Mean generation tokens/sec | 16.08 | 7.30 |
| Correct checked fields | 4/5 | 4/5 |
| Valid JSON | 5/5 | 5/5 |
| Lineup request seconds | 4.41 | 10.70 |
| News request seconds | 7.35 | 21.40 |
| Waiver request seconds | 5.65 | 192.34 |
| Abstention request seconds | 3.46 | 8.41 |
| Arithmetic request seconds | 15.78 | 11.97 |
| NutsNews health p95 milliseconds | 7.75 | 3.39 |
| Health errors observed | 0 | 0 |

Request times include load/queue overhead. The first 3B request reported 12.77 seconds loading; the first 7B request 5.18 seconds. These are not controlled cold-start comparisons. The 7B waiver request reported 174.42 seconds in load duration while a separate read showed production 3B loaded and active. This is consistent with shared scheduling/model-switch contention, not 192 seconds of token generation alone. Health p95 differences do not establish a speed improvement; unequal sample counts and live traffic confound that comparison. Health checks do not measure NutsNews article-generation latency.

Both models failed arithmetic: correct answer 18; 3B returned 38, 7B returned 6.8. Both selected legal lineup B and eligible waiver A, rejected a guarantee from unverified news, and returned null for an unsupported forecast. Manual review found 7B more explicit about the unavailable rostered waiver candidate and Reddit's lack of evidence. This does not establish broad superiority or hallucination rates.

## Decision

Keep 3B in production. 7B fits comfortably in memory but generated about 2.2 times slower with these settings, with additional shared-server delay. Continue deterministic point calculations and evidence-backed Qwen commentary. A larger model is not demonstrated to meet the requirement of avoiding slowdown. Further evaluation would need repeated representative long prompts and matched NutsNews generation latency measurements during a planned low-traffic window.
