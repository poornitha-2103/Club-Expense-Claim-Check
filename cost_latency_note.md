# Cost and Latency Note

**Author:** Poornitha K  

## Assumptions
- Typical college day: 15–25 new expense claims.
- Model used: free-tier / low-cost chat model (e.g. GPT-4o-mini or equivalent) via the Week-12 API.
- Average prompt size ≈ 450 tokens (system prompt + one claim).
- Average completion ≈ 60 tokens.

## API Calls per Day
One LLM call per claim → **15–25 calls/day**.

## Latency
- Measured round-trip (local network): 1.2–2.8 seconds per call.
- Total wall-clock time for a 20-claim batch: ≈ 30–55 seconds (sequential). Parallelisation possible but not required for this volume.

## Estimated Cost
Using published free-tier / low-cost rates (≈ $0.15 / 1M input tokens, $0.60 / 1M output tokens):

| Item | Tokens | Cost |
|------|--------|------|
| 20 claims × 450 input | 9 000 | < $0.002 |
| 20 claims × 60 output | 1 200 | < $0.001 |
| **Daily total** | | **≈ $0.003** |

Even at 100 claims/day the cost remains well under $0.02. The free-tier allowance is more than sufficient for the entire semester.

## Conclusion
Cost and latency are negligible for the intended daily volume. The dominant constraint is correctness (fact-checking + human signature), not money or speed.
