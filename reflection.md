# 400-Word Reflection

**Author:** Poornitha K  

Building the Club Expense Claim Checker taught me more about the limits of language models than about their strengths. The most important lesson was that extraction is not the same as verification. Even when the model returned clean JSON, the amount or the bill flag could be wrong. Treating the LLM as an untrusted source and forcing every claim through a deterministic fact-check layer turned out to be the single most valuable design decision of the whole project.

I was surprised by how often the model wanted to calculate. Notes such as “500 per person for 6” produced a total of 3000 instead of null. The red-team cases made this failure mode impossible to ignore. Adding an explicit “never calculate” constraint inside the system prompt and then verifying in Python that the returned number literally appears in the original note closed that loophole. Without the second check, the tool would have been quietly incorrect on many real-world claims.

The decision-rule order also mattered more than I expected. Putting `needs_more_info` and `needs_bill` before any budget or approval check meant that incomplete claims never polluted the running totals. Only after a claim survived every earlier gate was its amount added to the club’s dictionary keyed by `(club, category)`. That simple ordering prevented a subtle class of bugs that would have been hard to spot later.

Working with CSV files that contain commas inside quoted fields reminded me that “simple” data formats still need care. Python’s csv module handled the quoting correctly once I stopped trying to split the lines myself. Small details such as date ordering and consistent category names also affected whether the split-claim detection worked reliably.

On the human side, writing the Judgment Framework forced me to think carefully about asymmetric costs. A false “ok_to_pay” creates direct financial risk, while a false “review” only creates delay. The system is therefore deliberately biased toward caution. The faculty treasurer’s physical signature after seeing the original bill remains the real safety net; the tool is only an advisory filter that reduces the treasurer’s workload.

Finally, keeping the API key out of the repository, writing a clear specification before coding, and documenting every major decision in the AI Decision Journal made the project feel professional rather than experimental. I now understand that a reliable AI-assisted system is less about clever prompting and more about clear contracts between components, defensive code, thorough evaluation, and an explicit human checkpoint that cannot be bypassed.
