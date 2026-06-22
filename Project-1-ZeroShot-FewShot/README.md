# Project 1 — Zero-Shot & Few-Shot Structured Data Extraction

> Extracting clean, validated JSON from unstructured customer support text using zero-shot and few-shot prompting techniques.

![Status](https://img.shields.io/badge/status-complete-brightgreen) ![Technique](https://img.shields.io/badge/technique-zero--shot%20%2F%20few--shot-blue) ![Output](https://img.shields.io/badge/output-strict%20JSON-orange)

---

## 1. Project Overview

Customer support teams generate thousands of unstructured messages every day — emails, chat transcripts, ticket descriptions — that contain valuable structured information buried inside free-form text: customer name, order ID, issue category, sentiment, urgency, and requested resolution.

This project demonstrates how to design a **production-grade extraction prompt** that converts raw, messy customer support text into **strictly valid JSON**, using two complementary prompting strategies:

- **Zero-shot prompting** — the model extracts data from a precise instruction and schema alone, with no examples.
- **Few-shot prompting** — the model is shown 3–5 worked examples (including edge cases) to lock in formatting, tone, and null-handling behavior before being asked to generalize to new inputs.

The end deliverable is a single **final, hardened prompt** (`final_prompt.md`) that can be pasted directly into ChatGPT, Claude, Gemini, or any other LLM and reliably returns parseable JSON, every time.

---

## 2. Objectives

- Design a zero-shot extraction prompt and identify its failure modes.
- Upgrade the prompt to few-shot to eliminate those failure modes.
- Enforce a strict, documented JSON schema with explicit null-handling rules.
- Use delimiters to separate instructions, schema, examples, and input text unambiguously.
- Set `temperature = 0` (or equivalent) to maximize determinism and output consistency.
- Validate the JSON output of every test case against the schema.
- Document the entire process so it can be reused for any structured-extraction task.

---

## 3. Problem Statement

Large Language Models are excellent at understanding free text but, by default, are **inconsistent** at producing machine-readable output:

- They may wrap JSON in explanatory prose ("Sure! Here's the extracted data:").
- They may invent fields that don't exist in the schema.
- They may omit fields that have no value instead of returning `null`.
- They may use inconsistent key casing or string formatting across runs.
- They may "hallucinate" a value (e.g., guessing an order number) rather than admitting it is missing.

These inconsistencies break downstream systems (databases, ticketing pipelines, analytics dashboards) that expect a fixed schema. The goal of this project is to **engineer a prompt so precise that this failure class is effectively eliminated**, and to prove it with test cases.

---

## 4. Skills Learned

| Skill | Description |
|---|---|
| Zero-shot prompt design | Writing a complete, unambiguous instruction without examples |
| Few-shot prompt design | Selecting representative examples that teach edge-case behavior |
| Schema enforcement | Defining and validating a strict JSON contract |
| Delimiter discipline | Using clear separators (`###`, XML-style tags, triple backticks) to prevent prompt injection and ambiguity |
| Null-handling strategy | Deciding and documenting how missing information should be represented |
| Deterministic configuration | Using `temperature = 0` and related parameters for reproducibility |
| Output validation | Writing test cases that check structural and semantic correctness |
| Technical documentation | Producing professional, review-ready documentation for engineering work |

---

## 5. Technologies

- **LLM Targets:** OpenAI GPT-4 / GPT-4o, Anthropic Claude, Google Gemini (model-agnostic prompt design)
- **Format:** Markdown, JSON
- **Validation:** Manual schema validation (documented), compatible with `jsonschema` (Python) or `zod` (TypeScript) for automated pipelines
- **Recommended Inference Settings:** `temperature = 0`, `top_p = 1`, `max_tokens` sized to schema

---

## 6. Folder Structure

```
Project-1-ZeroShot-FewShot/
├── README.md                # This file
├── system_prompt.txt        # Reusable system-level instruction
├── examples.md              # Few-shot example bank (input → output pairs)
├── sample_input.txt         # Raw unstructured customer support message
├── expected_output.json     # Ground-truth JSON for the sample input
├── final_prompt.md          # The complete, hardened, ready-to-paste prompt
└── test_cases.md            # Test suite with inputs, expected outputs, and pass/fail criteria
```

---

## 7. Workflow

```
┌─────────────────────┐     ┌──────────────────────┐     ┌────────────────────────┐
│  1. Define Schema    │ ──▶ │ 2. Write Zero-Shot    │ ──▶ │ 3. Identify Failures   │
│  (fields, types,     │     │    Prompt             │     │ (missing nulls, prose, │
│   null rules)         │     │                       │     │  hallucinated fields)  │
└─────────────────────┘     └──────────────────────┘     └────────────────────────┘
                                                                       │
                                                                       ▼
┌─────────────────────┐     ┌──────────────────────┐     ┌────────────────────────┐
│ 6. Document & Ship   │ ◀── │ 5. Validate Against   │ ◀── │ 4. Add Few-Shot        │
│   final_prompt.md    │     │    Test Cases         │     │    Examples            │
└─────────────────────┘     └──────────────────────┘     └────────────────────────┘
```

---

## 8. Implementation Steps

1. **Schema definition.** Defined seven fields: `customer_name`, `order_id`, `issue_category`, `sentiment`, `urgency`, `requested_resolution`, and `summary`. Documented allowed enum values for `issue_category`, `sentiment`, and `urgency`.
2. **Zero-shot draft.** Wrote an instruction-only prompt (`system_prompt.txt`, v1) and ran it against five sample tickets. Observed three recurring failures: explanatory preambles, `"N/A"` strings instead of `null`, and inconsistent date formatting.
3. **Delimiter hardening.** Wrapped the input text in explicit `<customer_message>` tags and the schema in a fenced code block so the model could never confuse instruction text with data to extract.
4. **Few-shot expansion.** Added four worked examples in `examples.md`, deliberately including one ticket with a missing order ID (to teach `null` handling) and one angry, sentiment-heavy message (to teach calibrated sentiment classification).
5. **Determinism lock-in.** Specified `temperature = 0` and instructed the model to output **only** the JSON object, with no surrounding text, to make the output directly parseable.
6. **Validation.** Ran the final prompt against the test suite in `test_cases.md` and confirmed 100% schema compliance and correct null-handling across all cases.

---

## 9. Testing

Testing followed a three-tier approach:

| Tier | Focus | Example |
|---|---|---|
| **Structural** | Is the output valid, parseable JSON with exactly the expected keys? | No extra/missing keys, correct types |
| **Semantic** | Does the extracted value correctly reflect the input text? | Sentiment matches tone, urgency matches stated impact |
| **Edge Case** | Does the model correctly handle absent or ambiguous information? | Missing order ID → `null`, not a guessed value |

See [`test_cases.md`](./test_cases.md) for the full suite of 8 test cases and their results.

---

## 10. Expected Output

For the sample input in `sample_input.txt`, the prompt must return **exactly** the JSON shown in `expected_output.json` — a single, minified-or-pretty JSON object with no commentary, no markdown code fences in the actual API response, and all fields present (using `null` where data is unavailable).

---

## 11. Real-World Applications

- **Customer support automation** — auto-tagging and routing tickets by category and urgency.
- **CRM enrichment** — populating structured fields from free-text support emails.
- **Analytics pipelines** — feeding sentiment and issue-category data into dashboards.
- **Chatbot handoff** — extracting structured context before escalating to a human agent.
- **Compliance logging** — generating consistent structured records for audit trails.

---

## 12. Learning Outcomes

- Zero-shot prompts are a fast way to *discover* failure modes, not a reliable way to *ship* extraction systems.
- Few-shot examples are most valuable when they demonstrate **edge cases**, not just the "happy path."
- Explicit delimiters around user-provided text materially reduce prompt-injection and parsing ambiguity.
- A documented null-handling policy is as important as the schema itself — undocumented null behavior is a common source of downstream bugs.
- Output-format constraints ("return only JSON, no commentary") must be stated explicitly and reinforced in the system prompt, not assumed.

---

## 13. Future Improvements

- Add automated `jsonschema` validation as a CI step for any future test cases.
- Extend the schema to support multi-issue tickets (an array of issue objects per message).
- Add multilingual test cases to validate extraction from non-English support messages.
- Benchmark output consistency across multiple LLM providers at `temperature = 0`.
- Introduce a confidence score field for each extracted value to support human-in-the-loop review.

---

## 14. Professional Conclusion

This project demonstrates the complete lifecycle of designing a structured-extraction prompt for a real-world enterprise use case: starting from a naive zero-shot draft, diagnosing its concrete failure modes, and iterating toward a hardened, few-shot, schema-enforced prompt that produces deterministic, validated JSON. The techniques shown here — delimiter discipline, explicit null-handling, and edge-case-driven few-shot example selection — are directly transferable to any production system that needs to convert unstructured text into reliable structured data.
