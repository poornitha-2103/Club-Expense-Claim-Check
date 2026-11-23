# Judgment Framework – Club Expense Claim Checker

**Author:** Poornitha K  

| Component | What could go wrong? | Who is harmed & how? | Human checkpoint / mitigation |
|-----------|----------------------|----------------------|-------------------------------|
| **LLM Extraction** | LLM invents an amount or marks bill=true when none is mentioned | Club may be over-paid or a claim without proof may reach treasurer | Python fact-check (`numbers_in` + bill keyword rules) rejects the extraction before any decision is made |
| **Fact-checking layer** | Bug allows a calculated total (“500 × 6”) to pass | Incorrect payment amount | Unit tests + red-team cases that force calculation; amount must literally appear in note |
| **Decision rules** | Claim wrongly marked `ok_to_pay` | Money leaves the club budget without proper scrutiny | Faculty treasurer must still physically sign every payment after seeing the original bill |
| **Decision rules** | Claim wrongly sent to `review` (false positive) | Legitimate expense is delayed | Treasurer can still approve after reviewing the note; delay is recoverable |
| **Budget tracking** | Non-`ok_to_pay` claims are added to running totals | Budget appears exhausted when it is not | Explicit rule: only `ok_to_pay` claims update the dictionary of running totals |
| **Split-claim detection** | Two small claims on same day / category slip through | Effective spend > ₹5 000 without approval | Same-day + same-club + same-category accumulation check before `ok_to_pay` |
| **Output files** | `payable.csv` contains a claim that later fails human review | Temporary incorrect signal to treasurer | Treasurer always sees the physical bill; tool is advisory only |

### Key Trade-off Comparison
- **False OK (claim paid that should not be)** → Direct financial risk.  
- **False Review (good claim held back)** → Operational delay only.  

Therefore the system is deliberately biased toward caution. The final human checkpoint — the faculty treasurer who signs every payment after seeing the bill — remains the ultimate safety net.
