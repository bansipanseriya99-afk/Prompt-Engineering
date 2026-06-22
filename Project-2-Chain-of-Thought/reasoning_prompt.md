# The Reasoning Framework — Understand → Plan → Execute → Verify

This document explains the design rationale behind the structured Chain-of-Thought framework used throughout this project, and provides the complete, ready-to-paste prompt template.

---

## Why Naive CoT Isn't Enough

The classic CoT trigger phrase — *"Let's think step by step"* — reliably improves performance over zero-shot prompting on multi-step problems. But it has a structural weakness: **the model's reasoning is linear and unaudited.** Once an error is introduced in an early step, nothing in the prompt instructs the model to go back and check it. The model simply continues reasoning *forward* from a flawed premise, often arriving at a wrong answer stated with full confidence.

The fix is not "more reasoning" — it's **structured reasoning with a built-in feedback loop.**

---

## The Four Phases

### Phase 1 — Understanding
**Purpose:** Force the model to fully parse the problem before attempting to solve it. This phase alone eliminates a surprising number of errors caused by misreading the question (e.g., solving for the wrong variable, missing a constraint).

**What good Understanding looks like:**
- A restatement of the problem in different words than the original.
- An explicit list of "given" information.
- An explicit statement of what's actually being asked.
- Flagging of any ambiguity.

### Phase 2 — Plan
**Purpose:** Separate *strategy* from *execution*. By committing to a plan before doing any calculation, the model is less likely to course-correct mid-calculation in a way that silently discards earlier (possibly correct) work.

**What good Planning looks like:**
- A numbered list of steps.
- Explicit identification of which formula, algorithm, or logical rule applies.
- No actual computation yet — purely strategic.

### Phase 3 — Execution
**Purpose:** Carry out the plan with full transparency. Every intermediate value should be visible, not collapsed into a single leap to the answer.

**What good Execution looks like:**
- Each step from the Plan is carried out in order.
- Intermediate results are stated explicitly (not just the final number).
- Assumptions are flagged inline if any are made.

### Phase 4 — Verification (the critical addition)
**Purpose:** This is the phase that distinguishes structured CoT from naive CoT. Verification must be **independent** of Execution — re-deriving the answer a different way, or testing it against the original constraints — not simply re-reading and agreeing with the Execution phase.

**What good Verification looks like:**
- A genuinely different checking method than the one used in Execution (e.g., if Execution solved algebraically, Verification substitutes the answer back into the original equation).
- Explicit pass/fail logic: "Substituting back: 3(14) - 8 = 34 ✓ matches the given total."
- If a discrepancy is found, the model must say so explicitly and correct course.

### Final Answer
Only stated after Verification. Must reflect any corrections made during Verification — never a pre-correction value.

---

## The Complete Prompt Template

```text
You are a rigorous reasoning engine. Solve the following problem using exactly this structure:

**1. Understanding**
Restate the problem. Identify what's given and what's being asked. Flag any ambiguity.

**2. Plan**
List, in order, the steps you will take to solve this. Identify the method/formula/strategy. Do not calculate yet.

**3. Execution**
Carry out each planned step explicitly, showing all intermediate work.

**4. Verification**
Independently re-check your Execution result using a DIFFERENT method (re-derive a different way, substitute back into the original problem, or test edge cases). If you find a discrepancy with Execution, explicitly state it, explain the error, and correct it.

**Final Answer**
State your final, post-verification answer on its own line as "Final Answer: ..."

Problem:
[INSERT PROBLEM HERE]
```

---

## Why This Generalizes Across Domains

The four-phase structure doesn't depend on the subject matter — it depends only on the idea that **solving** and **checking** should be cognitively separate acts:

| Domain | Execution Looks Like | Verification Looks Like |
|---|---|---|
| Math | Algebraic manipulation, arithmetic | Substituting the answer back into the original equation |
| Programming | Writing/tracing code logic | Manually tracing the code against given test inputs |
| Logic puzzles | Applying deduction rules in sequence | Checking the final solution satisfies every original constraint |
| Interview questions | Walking through an algorithmic approach | Checking time/space complexity claims and edge cases (empty input, single element, duplicates) |

This is why the same template is reused, unmodified in structure, across all four challenge categories in `challenge_questions.md`.
