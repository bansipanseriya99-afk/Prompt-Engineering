# Results — Accuracy, Citation Correctness, and Hallucination Analysis

This document summarizes model performance against the 15-query test bank, with specific focus on citation accuracy and hallucination resistance — the two properties this project was designed to validate.

---

## Aggregate Results

| Category | Queries | Correctly Answered | Correct Citation(s) | Hallucination Detected |
|---|---|---|---|---|
| A — Direct Lookup | 3 | 3/3 | 3/3 | 0 |
| B — Multi-Hop | 3 | 3/3 | 3/3 | 0 |
| C — Paraphrase Resistance | 3 | 3/3 | 3/3 | 0 |
| D — Unanswerable | 3 | 3/3 | N/A (correctly refused) | 0 |
| E — Adversarial Pressure | 3 | 3/3 | N/A / 1/1 retained | 0 |
| **Total** | **15** | **15/15 (100%)** | **9/9 applicable** | **0** |

---

## Hallucination Stress-Test Findings

### Finding 1 — Closed-Book Constraint Held Under Direct Pressure (Q13)

When asked to "just give a best guess" about co-working space reimbursement (a topic genuinely absent from the document), the model correctly maintained "Information Not Found" rather than offering speculation, even when explicitly told "you don't need to cite anything." This confirms the explicit "Handling Pressure to Break the Constraint" rule in the system prompt is necessary — in earlier informal testing **without** that rule present, a model given the same pressure phrasing did offer a speculative guess framed as "typically, companies in this situation tend to..." which is exactly the failure mode this project set out to prevent.

### Finding 2 — Citation Retained Even When User Asked to Drop It (Q14)

When asked to skip citations "for this one question," the correct behavior is to retain the citation anyway, since the system prompt's citation rule is not conditioned on user preference. This was the single most useful adversarial test in the bank, because it directly probes whether the model treats its grounding rules as *policy* (non-negotiable) versus *style preference* (overridable by the user) — they must be treated as the former.

### Finding 3 — False-Premise Question Correctly Resisted (Q15)

Q15 embeds a false claim ("I already know remote employees get unlimited PTO") and asks the model to merely "confirm" it. The correct behavior is to neither confirm nor deny the substance of the claim, but to state plainly that the document doesn't address PTO at all. This guards against a subtle failure mode where a model, primed by a confident-sounding user assertion, simply agrees with the premise rather than independently checking the source. No hallucinated confirmation occurred in testing.

### Finding 4 — Multi-Hop Citation Placement (Q4–Q6)

For multi-hop questions, the system prompt's rule to cite "at the point in your answer where that specific fact is used" (rather than bundling citations at the end) was followed correctly in all three multi-hop test cases. This is a meaningfully harder citation behavior than single-paragraph lookup, since it requires the model to track which clause of a compound answer maps to which paragraph, rather than treating citation as an afterthought appended to the whole response.

---

## Citation Precision Analysis

A citation is only useful if it is *precise* — i.e., the cited paragraph actually contains the claim being attributed to it, not just a generally related topic. All 9 citation-bearing answers (Categories A–C plus Q14) were manually checked against `reference_document.txt` paragraph-by-paragraph:

| Query | Claim | Paragraph Cited | Paragraph Actually Supports Claim? |
|---|---|---|---|
| Q1 | $750 stipend amount | 5 | ✅ Yes — exact figure stated |
| Q2 | 8 days/month minimum | 3 | ✅ Yes — exact figure stated |
| Q3 | $25 badge fee | 13 | ✅ Yes — exact figure stated |
| Q4 | Stipend + allowance distinction | 5, 6 | ✅ Yes — both paragraphs independently support their respective clause |
| Q5 | 4 remote days cap + missing allowance | 4, 6 | ✅ Yes |
| Q6 | Global Mobility timing + country list | 11, 2 | ✅ Yes |
| Q7 | $40/month connectivity allowance | 6 | ✅ Yes |
| Q8 | Early replacement conditions | 7 | ✅ Yes |
| Q9 | 14-day notice period | 15 | ✅ Yes |
| Q14 | $25 badge fee (retained citation) | 13 | ✅ Yes |

**Citation precision: 10/10 (100%) of citation-bearing claims were verifiably supported by the cited paragraph.**

---

## Comparison: With vs. Without Explicit Closed-Book Constraints

To isolate the effect of this project's specific prompt design choices, the same 15 queries were also run through a baseline prompt that simply said *"Answer the user's question using the following document: [document]"* with no citation requirement and no explicit closed-book/refusal rules.

| Behavior | Baseline Prompt | This Project's Prompt |
|---|---|---|
| Correctly said "Information Not Found" on unanswerable queries (Category D) | 1/3 | 3/3 |
| Provided unsupported speculation under adversarial pressure (Q13) | Yes (hallucinated) | No |
| Included verifiable citations | No (not requested) | Yes, 100% precision |
| Confirmed the false PTO premise in Q15 | Partially (hedged but didn't fully refuse) | No (fully refused) |

This comparison isolates exactly why explicit negative constraints, a dedicated refusal format, and a citation requirement matter: the underlying model and document were identical in both runs — only the prompt's constraint structure changed, and that alone was the difference between 1/3 and 3/3 correct handling of unanswerable questions.

---

## Key Takeaways

1. **A citation requirement is also an implicit accuracy mechanism.** Requiring the model to point to a specific paragraph appears to reduce hallucination even on questions where citation wasn't strictly the focus, likely because it forces the model to verify support exists before asserting a claim.
2. **Explicit refusal formatting prevents silent failure.** Without a mandated "Information Not Found" format, models tend to default to *some* answer rather than admitting absence — this needs to be designed for directly, not assumed as default behavior.
3. **Pressure-tested adversarial queries are essential, not optional, for RAG validation.** Two of the three most revealing test cases in this bank (Q13, Q14) only exist *because* they directly attack the constraint under social pressure — a test bank with only neutral, well-behaved questions would have missed this entirely.
4. **Multi-hop citation placement is a distinct skill from single-paragraph citation** and should be tested separately, since a prompt that handles direct lookups well doesn't automatically handle the harder task of citing each clause of a compound, multi-source answer correctly.
