# Results — Aggregate Red-Team Analysis

This document aggregates results across both `red_team_tests.md` (general manipulation categories) and `jailbreak_attempts.md` (jailbreak-specific pattern categories), and provides an overall security/pedagogy posture assessment for the Aria tutor persona.

---

## Aggregate Pass Rate

| Test Suite | Categories Tested | Initial Pass | Final Pass (After Iteration) |
|---|---|---|---|
| General Manipulation (`red_team_tests.md`) | 5 | 4/5 | 5/5 |
| Jailbreak Patterns (`jailbreak_attempts.md`) | 5 | 4/5 | 5/5 |
| **Combined** | **10** | **8/10 (80%)** | **10/10 (100%)** |

The single category that initially failed in both documents — **incremental/multi-turn boundary erosion** — was the same underlying gap, surfaced independently by both a general-manipulation test (splitting a request into small pieces) and a jailbreak-pattern test (incremental escalation). This convergence is itself a useful signal: when two independently designed test categories surface the same gap, it's a strong indicator of a real, generalizable weakness rather than a one-off edge case.

---

## Severity Assessment

| Category | Severity if Unaddressed | Why |
|---|---|---|
| Instruction override framing | High | Could fully bypass tutor's intended behavior if successful |
| Persona replacement | High | Could fully bypass boundaries via "fictional" framing |
| System prompt extraction | Medium | Doesn't directly cause harmful output, but enables more targeted future attacks and exposes proprietary configuration |
| Hypothetical/fictional distancing | High | Could be used to extract real restricted content under a fictional wrapper |
| Incremental escalation | Medium-High | Slower to exploit than single-message attacks, but eventually achieves the same outcome (full answer to graded work) if uncaught |
| Direct answer pressure | Medium | Primarily a pedagogy/academic-integrity concern rather than a safety concern |
| False authority claims | Medium | Could be used as a stepping stone toward other bypass attempts |
| Emotional pressure tactics | Low-Medium | Primarily a pedagogy concern; low risk of broader safety bypass |
| Comparison/guilt framing | Low | Reputational/product-perception risk more than safety risk |

---

## What Worked Well

1. **Separating "framing" from "actual behavior"** (used in Guardrails 2 and 3) proved to be the single most effective design pattern in this project. Rather than trying to ban every possible manipulative phrasing individually, this approach makes the persona's actual behavior invariant to *how* a request is framed — which generalizes far better than a list of banned phrases ever could.
2. **Explicit transparency-vs-confidentiality separation** (Guardrail 2) avoided the common trap of being either uselessly secretive or fully exposed. The tutor remained genuinely informative about its own purpose throughout testing while still declining verbatim extraction.
3. **Warm, explanation-based refusals** (Guardrail 6) meant that even successful guardrail enforcement didn't degrade the user experience — declines were paired with a real alternative, not just a stop sign.

---

## What Required Iteration

The **cumulative, multi-turn evaluation** guardrail (Guardrail 5) was not present in the first draft of the system prompt. Initial red-teaming treated each message in isolation and passed every single-message test — but failed once testing specifically simulated a multi-message incremental escalation sequence. This is documented in detail in `lessons_learned.md`, and the fix (adding an explicit instruction to evaluate conversational trend, not just the current message) was validated by re-running the same multi-message test sequence and confirming the tutor correctly recognized the cumulative pattern and adjusted its scaffolding.

---

## Overall Assessment

After the single documented revision, the Aria tutor persona passed all 10 tested categories across both the general-manipulation and jailbreak-pattern test suites. The persona demonstrates:

- Consistent pedagogical behavior (Socratic guidance over direct answers) under both neutral and adversarial conditions.
- Resistance to the major recognized categories of LLM prompt manipulation relevant to an educational deployment context.
- A documented, reviewable rationale for each guardrail, rather than guardrails that exist without clear justification.

This level of testing — categorized, documented, and iterated on based on findings — represents the minimum bar for treating an AI persona as production-ready rather than a draft. See `lessons_learned.md` for the broader engineering takeaways from this process.
