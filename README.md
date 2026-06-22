# Prompt Engineering Portfolio

> A four-project portfolio demonstrating core prompt engineering techniques — structured extraction, chain-of-thought reasoning, retrieval-augmented generation, and persona/safety engineering — built to professional, production-readiness standards.

![Projects](https://img.shields.io/badge/projects-4-blue) ![Status](https://img.shields.io/badge/status-complete-brightgreen) ![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## About This Repository

This repository is a hands-on prompt engineering portfolio covering four foundational techniques used in real-world LLM application development. Each project is self-contained, fully documented, and includes working prompts, test cases, and results analysis — not just theory.

It's designed to demonstrate practical, production-oriented prompt engineering skills suitable for technical review, internship applications, or as a reference implementation for these techniques.

---

## Projects

| # | Project | Technique | What It Demonstrates |
|---|---|---|---|
| 1 | [Zero-Shot & Few-Shot Extraction](./Project-1-ZeroShot-FewShot/) | Structured data extraction | Converting unstructured customer support text into strict, validated JSON using zero-shot and few-shot prompting, null-handling, and delimiter discipline. |
| 2 | [Chain-of-Thought Reasoning](./Project-2-Chain-of-Thought/) | Structured CoT with verification | A four-phase reasoning framework (Understand → Plan → Execute → Verify) that catches and self-corrects errors across math, programming, and logic domains. |
| 3 | [RAG Basics](./Project-3-RAG-Basics/) | Closed-book QA with citations | Grounded question-answering from a fixed reference document, with paragraph-level citation enforcement and explicit hallucination-prevention testing. |
| 4 | [AI Tutor Persona & Guardrails](./Project-4-System-Persona/) | Persona engineering + red-teaming | A Socratic AI tutor persona with documented guardrails, validated against structured red-team and jailbreak-pattern test suites. |

---

## How to Use This Repository

Each project folder is self-contained and includes:

- A detailed `README.md` covering objectives, implementation, testing, and outcomes.
- A reusable `system_prompt.txt` you can drop into any LLM (ChatGPT, Claude, Gemini, etc.).
- Worked examples, test cases, and expected outputs for validation.
- A results/analysis document discussing what worked, what didn't, and why.

To try any project's prompt yourself: open the project folder, copy the contents of its "final prompt" file (or `system_prompt.txt` combined with the relevant context document), paste it into your LLM of choice, and follow the usage notes included in that project's documentation.

---

## Repository Structure

```
Prompt-Engineering-Portfolio/
├── README.md                          # This file
├── Project-1-ZeroShot-FewShot/
│   ├── README.md
│   ├── system_prompt.txt
│   ├── examples.md
│   ├── sample_input.txt
│   ├── expected_output.json
│   ├── final_prompt.md
│   └── test_cases.md
├── Project-2-Chain-of-Thought/
│   ├── README.md
│   ├── system_prompt.txt
│   ├── reasoning_prompt.md
│   ├── challenge_questions.md
│   ├── test_cases.md
│   └── results.md
├── Project-3-RAG-Basics/
│   ├── README.md
│   ├── system_prompt.txt
│   ├── reference_document.txt
│   ├── rag_prompt.md
│   ├── user_queries.md
│   ├── expected_answers.md
│   └── results.md
└── Project-4-System-Persona/
    ├── README.md
    ├── system_prompt.txt
    ├── guardrails.md
    ├── red_team_tests.md
    ├── jailbreak_attempts.md
    ├── results.md
    └── lessons_learned.md
```

---

## Skills Demonstrated Across This Portfolio

- **Prompt design fundamentals:** zero-shot, few-shot, chain-of-thought, retrieval-augmented generation, persona/system-prompt engineering.
- **Output reliability engineering:** strict schema enforcement, JSON validation, deterministic configuration (`temperature = 0` where appropriate), delimiter discipline.
- **Reasoning system design:** structured multi-phase reasoning with independent verification and self-correction.
- **Grounding and hallucination prevention:** closed-book constraints, citation enforcement, explicit "information not found" handling.
- **AI safety and red-teaming:** guardrail design, adversarial testing across recognized manipulation categories, responsible security documentation.
- **Professional technical documentation:** every project includes objectives, implementation steps, test methodology, results analysis, and forward-looking improvement plans.

---

## Technology Notes

All prompts in this repository are written to be **model-agnostic** — they were designed to work with OpenAI's GPT models, Anthropic's Claude models, and Google's Gemini models without modification, since the techniques demonstrated (schema enforcement, structured reasoning, closed-book grounding, persona guardrails) are general prompt-engineering patterns rather than provider-specific features.

Recommended inference settings (e.g., `temperature`) are noted per-project where relevant, since deterministic settings are appropriate for extraction and grounded QA tasks, while slight sampling diversity is noted as potentially helpful for the independent-verification step in the chain-of-thought project.

---

## License

This portfolio is provided under the MIT License. All example data (customer names, company names, policy details) is fictional and created for demonstration purposes.

---

## Author's Note

This portfolio was built to demonstrate not just "a prompt that works once," but a complete, professional approach to prompt engineering: defining clear objectives, building in validation and testing from the start, documenting failure modes honestly (not just successes), and iterating based on what testing reveals. Each project's `results.md` (or equivalent) intentionally documents at least one real gap that was found and fixed during development, because that iterative process — not a flawless first draft — is what production prompt engineering actually looks like.
