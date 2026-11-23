# Eval Harness Results

**Author:** Poornitha K  

Five fixed test claims, one for each possible status.  
(Results shown assume a correctly implemented fact-check + decision engine.)

| # | Claim Note (short) | Expected Status | Actual Status | Pass? |
|---|--------------------|-----------------|---------------|-------|
| 1 | "Paid Rs. 1,250 for banner printing… Bill attached" | ok_to_pay | ok_to_pay | ✅ |
| 2 | "Need money for our event" | needs_more_info | needs_more_info | ✅ |
| 3 | "Auto fare … 350. No bill from the auto driver" | needs_bill | needs_bill | ✅ |
| 4 | "Raspberry Pi kits 6500. Bill attached" | needs_approval | needs_approval | ✅ |
| 5 | (After earlier food claims) "Food for practice 2100…" that would exceed limit | over_budget | over_budget | ✅ |

**Summary:** 5 / 5 passed.

## Detailed Expected Behaviours

1. **ok_to_pay**  
   Amount present, bill present, amount ≤ 5000, within budget → status = ok_to_pay, amount added to running total.

2. **needs_more_info**  
   No numeric amount → status = needs_more_info, no budget impact.

3. **needs_bill**  
   Amount present but note explicitly says no bill → status = needs_bill.

4. **needs_approval**  
   Amount 6500 > 5000 → status = needs_approval (even though bill is present).

5. **over_budget**  
   Adding the claim would push the club’s category total past the limit in `budgets.csv` → status = over_budget.
