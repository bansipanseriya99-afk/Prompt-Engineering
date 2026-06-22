# Project 2 — Advanced Chain-of-Thought Reasoning

> An engineered reasoning framework that forces structured, verifiable, self-correcting thought before an LLM commits to a final answer.

![Status](https://img.shields.io/badge/status-complete-brightgreen) ![Technique](https://img.shields.io/badge/technique-chain--of--thought-blue) ![Domains](https://img.shields.io/badge/domains-math%20%7C%20code%20%7C%20logic-purple)

---

## 1. Project Overview

Chain-of-Thought (CoT) prompting improves LLM accuracy on multi-step problems by encouraging the model to reason through intermediate steps rather than jumping straight to a final answer. However, naive CoT ("think step by step") is inconsistent: models often skip verification, propagate early arithmetic or logical errors uncorrected, and present a wrong final answer with high apparent confidence.

This project builds an **advanced, structured CoT framework** that goes beyond "think step by step" by enforcing four explicit reasoning phases — **Understand → Plan → Execute → Verify** — with a mandatory self-correction step before the final answer is locked in. The framework is tested across three domains (mathematics, programming/debugging, and logic puzzles) plus a set of structured interview-style reasoning questions.

---

## 2. Objectives

- Design a reusable CoT reasoning template with explicit phase separation.
- Build in a mandatory verification/self-correction stage, not just linear reasoning.
- Apply the framework across math, programming, and logic-puzzle domains.
- Capture and analyze both successful reasoning chains and **failure cases**, including cases where verification catches and fixes an initial error.
- Document the measurable difference between naive CoT and structured CoT.

---

## 3. Problem Statement

Standard "think step by step" prompting has three recurring weaknesses observed during testing:

1. **Error propagation** — an early mistake (e.g., a miscalculated intermediate value) is carried forward unchecked into the final answer.
2. **False confidence** — the model states a final answer with the same confident tone whether the reasoning was sound or flawed, giving users no signal to distrust a bad answer.
3. **Reasoning/answer mismatch** — occasionally the stated reasoning steps support one conclusion, but the final answer line doesn't match the model's own derivation.

This project's structured framework directly targets these three weaknesses by separating planning from execution, and by requiring an explicit verification pass that can override the initial answer.

---

## 4. Skills Learned

| Skill | Description |
|---|---|
| Structured reasoning design | Decomposing "think step by step" into distinct, auditable phases |
| Self-correction prompting | Designing prompts that re-check their own work before finalizing |
| Cross-domain prompt generalization | Applying one reasoning template to math, code, and logic domains |
| Failure mode analysis | Systematically identifying *why* a reasoning chain failed |
| Confidence calibration | Teaching the model to flag uncertainty rather than hide it |
| Technical documentation | Communicating reasoning-system design decisions clearly |

---

## 5. Technologies

- **LLM Targets:** OpenAI GPT-4 / GPT-4o, Anthropic Claude, Google Gemini
- **Format:** Markdown
- **Recommended Inference Settings:** `temperature = 0.2–0.4` (slight diversity helps the verification stage catch errors; pure determinism can sometimes "lock in" an early mistake)

---

## 6. Folder Structure

```
Project-2-Chain-of-Thought/
├── README.md                # This file
├── system_prompt.txt         # Reusable structured CoT system instruction
├── reasoning_prompt.md       # The complete 4-phase reasoning framework, explained
├── challenge_questions.md    # Math, programming, logic, and interview questions used for testing
├── test_cases.md             # Full reasoning traces with verification steps
└── results.md                # Success/failure analysis and metrics
```

---

## 7. Workflow

```
┌──────────────┐    ┌───────────┐    ┌────────────┐    ┌────────────┐
│ 1. UNDERSTAND │ ──▶│ 2. PLAN   │ ──▶│ 3. EXECUTE │ ──▶│ 4. VERIFY  │
│ Restate the   │    │ Outline   │    │ Carry out  │    │ Re-check   │
│ problem &     │    │ solution  │    │ each step  │    │ each step; │
│ identify      │    │ steps     │    │ explicitly │    │ correct if │
│ what's asked  │    │ in order  │    │            │    │ needed     │
└──────────────┘    └───────────┘    └────────────┘    └─────┬──────┘
                                                              │
                                                   ┌──────────▼──────────┐
                                                   │ 5. FINAL ANSWER     │
                                                   │ (post-verification) │
                                                   └──────────────────────┘
```

---

## 8. Implementation Steps

1. **Baseline test.** Ran 5 multi-step math word problems through a naive "think step by step" prompt. Found 2/5 contained an arithmetic slip that was never caught.
2. **Framework design.** Defined four mandatory phases (Understand, Plan, Execute, Verify) and required the model to label each phase explicitly in its output, making the reasoning auditable.
3. **Verification design.** Specified that the Verify phase must independently re-derive the answer (or check it against the original constraints) rather than simply restating the Execute phase's conclusion — this is what catches propagated errors.
4. **Self-correction clause.** Added an explicit rule: if Verify contradicts Execute, the model must state the discrepancy, explain the correction, and only then give the final answer.
5. **Cross-domain testing.** Applied the same framework to programming/debugging tasks and logic puzzles to confirm the structure generalizes beyond arithmetic.
6. **Failure analysis.** Documented two cases where even the structured framework initially failed, analyzed root cause, and refined the Verify-phase instructions accordingly.

---

## 9. Testing

Each challenge question was run through both the **naive CoT prompt** and the **structured CoT prompt** for comparison. Results were scored on:

| Metric | Description |
|---|---|
| **Final Answer Correctness** | Does the stated final answer match ground truth? |
| **Reasoning Validity** | Is every intermediate step logically sound? |
| **Error Self-Correction** | If an error occurred during Execute, was it caught and fixed during Verify? |
| **Confidence Calibration** | Did the model flag uncertainty when a problem was genuinely ambiguous? |

See [`test_cases.md`](./test_cases.md) for full reasoning traces and [`results.md`](./results.md) for aggregate analysis.

---

## 10. Expected Output

Every response using the structured framework must contain four clearly labeled sections (`Understanding`, `Plan`, `Execution`, `Verification`) followed by a `Final Answer` line. The Verification section must show explicit re-checking work, not just a repeated assertion.

---

## 11. Real-World Applications

- **Automated tutoring systems** — showing students an auditable, step-by-step solution they can follow and learn from.
- **Code review assistants** — reasoning through a bug systematically rather than guessing at a fix.
- **Financial and engineering calculations** — where an unverified arithmetic error has real consequences.
- **Technical interview preparation tools** — modeling the kind of structured reasoning interviewers want to see.
- **Decision-support systems** — surfacing the reasoning trail for auditing and trust-building with end users.

---

## 12. Learning Outcomes

- A dedicated Verify phase is the single highest-leverage addition to standard CoT — it is what actually catches propagated errors, not just "more thinking."
- Forcing the model to *label* its reasoning phases (rather than reason freely) measurably improves auditability and makes failures easier to diagnose after the fact.
- Structured CoT generalizes well across very different domains (math, code, logic) because the Understand → Plan → Execute → Verify pattern doesn't depend on the subject matter.
- Some moderate sampling temperature can actually help the Verify phase behave as genuine independent re-derivation rather than a rubber-stamp restatement.
- Even a well-designed framework has edge cases; documenting *failures*, not just successes, is essential to making a reasoning system trustworthy.

---

## 13. Future Improvements

- Add automated answer-checking (e.g., executing generated code against unit tests, or checking math answers programmatically) to scale verification beyond manual review.
- Experiment with a dedicated "adversarial verifier" persona/prompt that actively tries to find flaws in the Execute phase's reasoning.
- Extend the challenge set to multi-turn reasoning (where context from earlier turns must be correctly carried forward).
- Add quantitative benchmarking against standard datasets (e.g., GSM8K-style math problems) to produce statistically robust accuracy comparisons.

---

## 14. Professional Conclusion

This project demonstrates that the value of Chain-of-Thought prompting lies not in asking a model to "think step by step," but in *engineering the structure of that thinking* — explicit phases, mandatory independent verification, and required self-correction when verification and execution disagree. Tested across mathematics, programming, and logic-puzzle domains, this structured framework consistently catches and corrects errors that naive CoT prompting allows to propagate silently into a confidently stated, incorrect final answer.
