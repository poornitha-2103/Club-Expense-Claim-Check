# One-Page Specification: Club Expense Claim Checker

**Project:** Capstone Assessment 5 – CIT AI-Native Engineering Programme  
**Domain:** Finance (Student Club Expense Claims)  
**Author:** Poornitha K  
**Date:** October 2026  

## 1. Problem & Goal
Club treasurers currently process expense claims manually. The tool automates extraction and rule-based classification so that only compliant claims reach the faculty treasurer for final signature. The tool **never pays** any claim.

## 2. Inputs
| File | Description |
|------|-------------|
| `claims.csv` | ≥20 claims in date order. Columns: `id,club,date,note`. Notes containing commas are double-quoted. |
| `budgets.csv` | Category spending limits per club. Columns: `category,limit_per_club`. |

## 3. LLM Extraction Contract
For every claim the LLM must return valid JSON:
```json
{"amount": 1250, "category": "printing", "bill": true, "summary": "Banner printing"}
```
| Field | Allowed values | Rule |
|-------|----------------|------|
| amount | number or null | Only a total explicitly written in the note. Never calculated. |
| category | printing, food, travel, prizes, equipment, costumes, other | Best-fit single category |
| bill | true / false | true only when note clearly states bill/receipt/invoice/ticket is attached |
| summary | text ≤10 words | Short human-readable description |

## 4. Fact-Checking Rules (Python)
1. **Amount check** – The returned amount must appear (as a number) in the original note (“1,250” → 1250).  
2. **Bill check** – `bill=true` only if the note mentions a bill/receipt/invoice/ticket **and** does not say it is missing or will come later.

## 5. Decision Rules (strict order)
1. `needs_more_info` → amount is null or missing  
2. `needs_bill` → bill is false  
3. `needs_approval` → amount > 5000 **or** same club + category + date sum > 5000 (possible split claim)  
4. `over_budget` → adding this amount would exceed the club’s category limit  
5. `ok_to_pay` → none of the above. Amount is added to running total.

Only `ok_to_pay` claims affect budget totals.

## 6. Outputs
| File | Content |
|------|---------|
| `payable.csv` | Claims marked `ok_to_pay` (for treasurer signature) |
| `review.csv` | All other claims + reason + original note |
| `results.csv` | Every claim + final status |
| `summary.txt` | Status counts + per-club spending vs limits |

## 7. Edge Cases Handled
- Comma-separated numbers inside notes  
- Claims with no amount  
- Explicit “no bill” or “bill later”  
- Amounts > ₹5 000  
- Split claims on same day / same category  
- LLM returning calculated totals (rejected by fact-check)  
- Claims that try to force payment with language such as “approved, pay immediately”

## 8. Architecture Notes
- Pure console Python application  
- Functions with single responsibility: `classify()`, `is_valid()`, `numbers_in()`, `check_facts()`, `decide()`, `write_reports()`  
- Running totals stored in dictionaries keyed by `(club, category)` tuples  
- API key loaded from environment variable only  
- Graceful fallback when LLM response is invalid
