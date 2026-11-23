# AI Decision Journal

**Author:** Poornitha K  

This journal records the major design and implementation decisions made while building the Club Expense Claim Checker, including where AI assistance was used and how the final code was verified.

## 1. Architecture Decision
**Choice:** Pure console Python with clear function boundaries (`classify`, `is_valid`, `numbers_in`, `check_facts`, `decide`, `write_reports`).  
**Reason:** Matches the required single-responsibility style taught in class and makes unit testing trivial.  
**AI role:** Used to sketch initial function signatures; all logic was rewritten and explained line-by-line by the author.

## 2. Fact-Checking Strategy
**Choice:** Never trust the LLM’s `amount` or `bill` fields. Always re-extract numbers from the original note and re-evaluate the bill flag with simple keyword rules.  
**Reason:** The red-team cases (calculated totals, “approved, pay immediately”) demonstrated that LLM output can be confidently wrong.  
**AI role:** Helped generate candidate keyword lists; final rules were hand-tuned against the 5 eval + 3 red-team cases.

## 3. Running Totals Data Structure
**Choice:** `dict` with tuple keys `(club, category)`.  
**Reason:** Clean, readable, and sufficient for the required per-club / per-category limits.  
**AI role:** Confirmed the pattern is idiomatic; no generated code was copied.

## 4. System Prompt Design
**Choice:** Strict 5-role framework (Authority, Exemplar, Constraint, Rubric, Metadata) with three concrete examples.  
**Reason:** Produces more reliable JSON and reduces hallucination of calculated amounts.  
**AI role:** Iterated on the prompt text; final version was frozen after the eval harness passed.

## 5. Error Handling
**Choice:** Any invalid LLM response → treat claim as `needs_more_info` and continue. Never crash.  
**Reason:** One bad reply must not stop the whole batch (common mistake listed in the spec).  
**AI role:** Suggested try/except patterns; final implementation was written and tested manually.

## 6. Git Hygiene
**Choice:** API key loaded only from environment variable; `.gitignore` explicitly excludes `.env` and any key files.  
**Reason:** Prevents the most common submission failure.  
**AI role:** None – standard practice from earlier weeks.

## Verification Principle
Every line of code submitted can be explained by the author. AI was used as a drafting and debugging aid, never as an unexamined source of logic.
