# PromptPilot Validation

## Background

This document defines how PromptPilot is evaluated.

When this issue was created, the intention was to define a methodology for validating the core product hypothesis: that guided conversation enables users to create significantly better AI prompts than starting from a blank prompt.

During implementation, a review of recent literature identified published research providing empirical evidence supporting this hypothesis. Consequently, this document focuses on evaluating PromptPilot's implementation rather than re-validating the underlying concept.

---

# External Validation

Recent research demonstrates that guided prompting assistants can improve user performance and the quality of AI-assisted work compared to conventional prompting.

PromptPilot therefore treats the effectiveness of guided conversational prompting as an externally supported assumption. The project's evaluation focuses on whether its own implementation successfully realizes that approach.

---

# Evaluation Goals

PromptPilot is evaluated against two independent objectives:

1. Output Quality
2. Conversation Quality

These objectives measure different aspects of the system and should be evaluated separately.

---

# Output Quality Evaluation

## Purpose

Determine whether PromptPilot produces prompts that lead to better AI outputs than the user's original prompt.

## Benchmark

Evaluation uses a curated benchmark consisting of real user prompts collected from publicly available prompt datasets.

The benchmark should:

- represent PromptPilot's supported task categories;
- include a mixture of under-specified, moderately specified, and already well-written prompts;
- remain immutable once published.

Additional benchmark cases must be introduced through versioned benchmark releases rather than modifying existing ones.

## Evaluation Procedure

For every benchmark case:

1. Use the original user prompt to generate an AI output.
2. Process the same task through PromptPilot.
3. Generate the PromptPilot prompt.
4. Generate an AI output using the same frontier language model.
5. Compare the two outputs using a blinded evaluation.

All evaluations within a benchmark run must use:

- identical model versions;
- identical inference parameters;
- identical evaluation protocol.

## Evaluation Models

Output evaluation should be performed using one or more frontier language models acting as judges.

Whenever practical, multiple judge models should be used to reduce dependence on a single model family.

## Evaluation Protocol

The protocol should satisfy the following principles:

- blind evaluation;
- randomized output order;
- pairwise comparison;
- predefined task-specific evaluation rubric;
- repeated evaluations when appropriate;
- fully automated execution.

Manual evaluation is optional and intended only for auditing automatic evaluations or investigating unexpected results.

---

# Conversation Quality Evaluation

## Purpose

Evaluate whether PromptPilot collects the right information with minimal user effort.

Conversation quality is evaluated independently of output quality.

A conversation should be assessed using metrics such as:

- number of questions asked;
- unnecessary questions asked;
- completeness of required information collected;
- missing required information;
- overall conversation efficiency.

The objective is not to maximize the amount of information collected, but to minimize unnecessary interaction while obtaining sufficient information to generate a high-quality prompt.

---

# Benchmark Design

The benchmark should represent realistic user tasks.

To reduce ceiling effects, it should intentionally include prompts with varying levels of initial quality.

Evaluation results should be analyzed by benchmark difficulty rather than relying solely on aggregate scores.

---

# Evaluation Procedure Stability

The evaluation procedure should remain stable as PromptPilot evolves.

To enable meaningful comparison over time:

- the benchmark dataset must be versioned;
- benchmark cases must remain unchanged after publication;
- the evaluation protocol must remain unchanged within a benchmark version;
- the evaluation rubric must remain unchanged within a benchmark version;
- the frontier language models used for generation and automatic evaluation must be recorded with every evaluation run.

Frontier language models evolve continuously and may become unavailable over time. Consequently, future evaluations are not expected to reproduce identical numerical results.

Instead, this methodology preserves the evaluation procedure while documenting the models used for each benchmark run. This allows future evaluations to follow the same process, even if newer frontier models are substituted.

---

# Future Work

Future versions of PromptPilot may supplement automated evaluation with human studies measuring:

- user satisfaction;
- perceived effort;
- perceived usefulness;
- task completion success.

These studies complement, rather than replace, the automated evaluation methodology defined in this document.

---

# References

1. Gutheil, N., Mayer, V., Müller, L., Rommelt, J., & Kühl, N. (2025).
   *PromptPilot: Improving Human-AI Collaboration Through LLM-Enhanced Prompt Engineering.*
   arXiv.
   https://arxiv.org/abs/2510.00555

2. Morris, M. R. (2024).
   *Prompting Considered Harmful.*
   Communications of the ACM, 67(12), 28–30.
   https://doi.org/10.1145/3673861

3. Zamfirescu-Pereira, J., Wong, R. Y., Hartmann, B., & Yang, Q. (2023).
   *Why Johnny Can't Prompt: How Non-AI Experts Try (and Fail) to Design LLM Prompts.*
   Proceedings of the ACM CHI Conference on Human Factors in Computing Systems.
   https://doi.org/10.1145/3544548.3581388

## Motivating references

4. Kraljic, T., & Lahav, M. (2024).
   *From Prompt Engineering to Collaborating: A Human-Centered Approach to AI Interfaces.*
   ACM Interactions.
   https://doi.org/10.1145/3649444