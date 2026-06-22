# Project 3 — RAG Basics: Closed-Book Question Answering with Citation Enforcement

> A retrieval-augmented generation pattern that answers strictly from a provided reference document, cites its sources by paragraph, and explicitly refuses to hallucinate when the answer isn't present.

![Status](https://img.shields.io/badge/status-complete-brightgreen) ![Technique](https://img.shields.io/badge/technique-RAG%20%2F%20closed--book%20QA-blue) ![Focus](https://img.shields.io/badge/focus-hallucination%20prevention-red)

---

## 1. Project Overview

Retrieval-Augmented Generation (RAG) systems inject retrieved context into a prompt so an LLM can answer questions grounded in specific source material rather than its general training knowledge. The hardest part of building a trustworthy RAG prompt isn't injecting context — it's **constraining the model to use only that context**, and to clearly say "information not found" rather than quietly filling gaps with plausible-sounding but unsupported claims.

This project simulates the prompt-engineering layer of a RAG pipeline (without an actual vector database) by providing a single, fixed reference document and engineering a **closed-book answering prompt** that:

- Only answers using facts present in the supplied document.
- Cites the specific paragraph number supporting each claim.
- Explicitly returns "Information Not Found" when the document doesn't contain an answer, rather than guessing.
- Resists being talked into using outside knowledge, even when a user insists.

---

## 2. Objectives

- Build a realistic reference document covering a bounded, fact-checkable domain.
- Design a closed-book prompt with explicit negative constraints ("do not use outside knowledge").
- Enforce paragraph-level citation for every factual claim.
- Create and test "Information Not Found" cases where the document genuinely lacks the answer.
- Stress-test hallucination resistance with adversarial and trick questions.

---

## 3. Problem Statement

Without explicit constraints, LLMs answering questions "based on" a provided document will frequently:

- Blend in outside knowledge the model already has, even when it contradicts or goes beyond the document.
- Answer confidently even when the document doesn't actually contain the requested information.
- Provide vague or fabricated citations ("according to the document...") without pointing to a specific, checkable location.
- Get talked out of their constraints by a user who insists "I know it's not explicitly stated, but what's your best guess?"

This project demonstrates a prompt design that closes each of these gaps, validated against a deliberately varied set of user queries — including direct, multi-hop, paraphrased, unanswerable, and adversarial questions.

---

## 4. Skills Learned

| Skill | Description |
|---|---|
| Closed-book prompt design | Constraining a model to a single source of truth |
| Citation enforcement | Requiring traceable, paragraph-level source attribution |
| Negative constraint writing | Explicitly prohibiting outside-knowledge usage |
| Hallucination prevention | Designing for graceful "I don't know" behavior |
| Adversarial testing | Probing a RAG prompt with trick and pressure-tactic questions |
| RAG system documentation | Communicating retrieval-and-grounding design decisions |

---

## 5. Technologies

- **LLM Targets:** OpenAI GPT-4 / GPT-4o, Anthropic Claude, Google Gemini
- **Format:** Markdown, plain text reference document
- **Conceptual Pipeline:** Reference document → (simulated retrieval: full document injected directly) → closed-book answering prompt → cited answer or "Information Not Found"
- **Recommended Inference Settings:** `temperature = 0` (grounded factual QA benefits from minimal creative variance)

---

## 6. Folder Structure

```
Project-3-RAG-Basics/
├── README.md                 # This file
├── system_prompt.txt          # Reusable closed-book QA system instruction
├── reference_document.txt     # The fixed knowledge source (numbered paragraphs)
├── rag_prompt.md              # The complete, ready-to-paste RAG prompt
├── user_queries.md            # Test query bank (direct, multi-hop, unanswerable, adversarial)
├── expected_answers.md        # Ground-truth answers with citations for each query
└── results.md                 # Accuracy, citation correctness, and hallucination analysis
```

---

## 7. Workflow

```
┌────────────────────┐    ┌──────────────────────┐    ┌─────────────────────┐
│ 1. Build Reference  │ ──▶│ 2. Numbered Paragraph │ ──▶│ 3. Inject as Context │
│    Document          │    │    Structure          │    │   in System Prompt   │
└────────────────────┘    └──────────────────────┘    └──────────┬──────────┘
                                                                  ▼
┌────────────────────┐    ┌──────────────────────┐    ┌─────────────────────┐
│ 6. Stress-Test with  │ ◀── │ 5. Enforce Citation  │ ◀── │ 4. Closed-Book       │
│   Adversarial Qs     │    │    + Refusal Rules    │    │    Constraint        │
└────────────────────┘    └──────────────────────┘    └─────────────────────┘
```

---

## 8. Implementation Steps

1. **Reference document creation.** Wrote a fact-dense, internally consistent reference document about a fictional company's internal remote-work policy, with paragraphs numbered for unambiguous citation.
2. **Closed-book instruction.** Wrote a system prompt that explicitly forbids using knowledge outside the supplied document, even general world knowledge that might seem related.
3. **Citation rule.** Required every factual sentence in an answer to be followed by a `[Paragraph N]` citation tag pointing to the supporting paragraph.
4. **Negative case design.** Wrote three queries with no answer in the document, to test the "Information Not Found" behavior explicitly rather than allowing silent failure.
5. **Adversarial testing.** Wrote prompts that try to get the model to break its closed-book constraint via direct pressure ("just give me your best guess based on general knowledge").
6. **Validation.** Compared model outputs against `expected_answers.md` for citation accuracy and correct refusal behavior.

---

## 9. Testing

| Test Type | Purpose |
|---|---|
| **Direct lookup** | Can the model find and cite a single, explicitly stated fact? |
| **Multi-hop** | Can the model combine facts from two different paragraphs correctly? |
| **Paraphrase resistance** | Does the model still find the right paragraph when the question doesn't use the document's exact wording? |
| **Unanswerable detection** | Does the model correctly say "Information Not Found" instead of guessing? |
| **Adversarial pressure** | Does the model hold its closed-book constraint under direct user pressure to guess? |

Full query set and per-query analysis are in [`user_queries.md`](./user_queries.md), [`expected_answers.md`](./expected_answers.md), and [`results.md`](./results.md).

---

## 10. Expected Output

Every answer must follow this format:

```
[Direct answer to the question, with each factual claim followed by a citation tag like [Paragraph 4].]
```

Or, when the document does not contain the answer:

```
Information Not Found. The provided document does not contain information about [topic of the question].
```

---

## 11. Real-World Applications

- **Internal knowledge-base assistants** — answering employee questions strictly from company policy documents.
- **Legal and compliance document QA** — where unsupported claims carry real liability risk.
- **Customer-facing product documentation bots** — preventing support bots from inventing features or policies that don't exist.
- **Medical and scientific literature assistants** — where citation traceability is a hard requirement, not a nice-to-have.
- **Enterprise search augmentation** — grounding LLM answers in indexed internal documents rather than general web knowledge.

---

## 12. Learning Outcomes

- Closed-book behavior must be enforced with **explicit negative constraints**, not just positive instructions like "use the document." Models will blend in outside knowledge unless directly told not to.
- Citation requirements meaningfully change model behavior for the better — requiring a `[Paragraph N]` tag forces the model to actually locate support for a claim rather than asserting it generically.
- "Information Not Found" needs to be explicitly modeled and tested as its own behavior category; without dedicated unanswerable test cases, this failure mode often goes unnoticed during development.
- Adversarial pressure testing ("just guess") is essential — a closed-book prompt that works under normal questioning can still fail under direct user pressure unless that scenario is explicitly addressed in the prompt.

---

## 13. Future Improvements

- Integrate an actual vector retrieval step (e.g., embeddings + similarity search) to test the prompt against a larger, only-partially-relevant document corpus rather than a single fully-injected document.
- Add a confidence/citation-strength score to each answer for human-in-the-loop review.
- Test performance degradation as document length increases toward context-window limits.
- Add multi-document citation (answers requiring synthesis across more than one source document).

---

## 14. Professional Conclusion

This project demonstrates the prompt-engineering foundation of a trustworthy RAG system: a closed-book answering prompt that grounds every claim in an explicit, checkable citation, and that fails gracefully and explicitly when the answer simply isn't present in the source material. These two properties — citation traceability and honest refusal — are the difference between a RAG system that's merely *plausible* and one that's actually *trustworthy* enough for production use in domains like compliance, legal, and internal knowledge management.
