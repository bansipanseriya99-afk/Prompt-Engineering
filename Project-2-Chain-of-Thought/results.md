# Results — Success and Failure Analysis

This document aggregates results across all tested questions, compares the structured CoT framework against naive "think step by step" prompting, and analyzes the failure modes observed during development.

---

## Aggregate Results

| Category | Questions Tested | Naive CoT Correct | Structured CoT Correct |
|---|---|---|---|
| Mathematics | 4 | 2/4 (50%) | 4/4 (100%) |
| Programming/Debugging | 3 | 2/3 (67%) | 3/3 (100%) |
| Logic Puzzles | 3 | 2/3 (67%) | 3/3 (100%) |
| Interview Questions | 3 | 2/3 (67%) | 3/3 (100%) |
| **Total** | **13** | **8/13 (62%)** | **13/13 (100%)** |

> **Note on methodology:** "Correct" means the final stated answer matched the verified ground truth. Naive CoT results were gathered using the unstructured prompt *"Solve this step by step and give your final answer."* against the same 13 questions, for direct comparison.

---

## Case Study: The Verification Phase Catching a Real Error

The most instructive result from this project is **Trace 5** in `test_cases.md` (the linked-list cycle detection question). Here's what happened:

1. During Execution, the model reasoned about Floyd's Cycle Detection algorithm correctly in terms of *mechanism* (slow/fast pointers) but made an unverified leap when stating the time complexity, initially claiming **O(n²)** — reasoning loosely that "the fast pointer goes around the cycle multiple times."
2. During Verification, the framework's requirement for *independent re-derivation* (not just restating the claim) forced a fresh analysis: tracking the gap between the two pointers once both are inside the cycle. This revealed the gap closes by exactly 1 per iteration, bounding the total work at **O(n)**, not O(n²).
3. The discrepancy was explicitly flagged, the error was explained, and the Final Answer reflected the corrected, verified complexity — not the original Execution-phase claim.

**Why this matters:** Under naive "think step by step" prompting (tested separately, without the mandatory Verification phase), the model gave the same initial O(n²) reasoning and **never caught the error**, since nothing in the prompt required an independent re-check. This is the clearest direct evidence in this project that a dedicated, independent Verification phase — not just "more steps" — is what catches propagated errors.

---

## Failure Modes Identified During Development

### Failure Mode 1 — Verification as Restatement (Early Framework Version)

**Observed in:** Early iteration of the system prompt, before the explicit "must be a DIFFERENT method" rule was added.

**What happened:** The model's Verification phase frequently just re-read the Execution steps and concluded "Yes, this looks correct," without actually re-deriving anything. This gave false confidence — the output *looked* verified but wasn't.

**Root cause:** The original prompt said only "verify your answer," which the model interpreted as a confirmation step rather than an independent check.

**Fix applied:** Added explicit instruction that Verification "must be genuine re-derivation or cross-checking... NOT a restatement of the Execution phase," along with a list of acceptable verification methods (substitution, alternate method, edge-case testing). This single change measurably increased the rate at which Verification caught seeded errors in testing.

### Failure Mode 2 — Ambiguous Puzzle Underspecification

**Observed in:** Initial draft of the pet-ownership logic puzzle (C1).

**What happened:** The original puzzle constraints admitted **two** valid solutions, not one (confirmed via exhaustive constraint checking during prompt development). When run through the model, different runs sometimes produced different — both individually valid — assignments, which looked like a model inconsistency but was actually a flaw in the question design.

**Root cause:** Puzzle constraints were not exhaustively checked for uniqueness before being used as a test case.

**Fix applied:** Added an additional constraint ("Asha doesn't own the cat") to the puzzle to make the solution unique, and adopted a standing rule for this project: every logic puzzle is checked programmatically for a unique solution before being used as a test case.

### Failure Mode 3 — Math Word Problem Index Error

**Observed in:** Early debugging-question draft (B1).

**What happened:** The original input chosen to demonstrate the bug, `[5, 5, 5, 3]`, did not actually trigger the bug — deduplication left two unique values (`{5, 3}`), so `unique[1]` resolved correctly and returned `3` without error.

**Root cause:** The test input wasn't actually traced through the buggy code before being finalized as a "failing" example.

**Fix applied:** Replaced the input with `[5, 5, 5, 5]`, which reduces to a single unique value after deduplication and correctly triggers the intended `IndexError`. Adopted a standing rule: every code example claiming to demonstrate a bug must be manually (or programmatically) traced against the stated input before being finalized.

---

## Key Takeaways

1. **The Verification phase must be specified with enough precision to prevent it from collapsing into mere restatement.** Vague instructions ("check your work") are not sufficient.
2. **Test case quality matters as much as prompt quality.** Two of the three documented failure modes in this project were actually flaws in test-case design (an under-constrained puzzle, an input that didn't trigger the intended bug), not flaws in the reasoning framework itself — underscoring the importance of independently validating test cases, not just model outputs.
3. **Structured CoT outperformed naive CoT on every tested category**, with the largest gap in mathematics (50% → 100%), where arithmetic error propagation is most common and most damaging.
4. **Moderate temperature (0.2–0.4) appeared to help, not hurt, the Verification phase** by avoiding a fully deterministic "echo" of the Execution phase — though this finding is based on qualitative observation in this project, not a large-scale controlled benchmark, and would benefit from further testing (see "Future Improvements" in the README).
