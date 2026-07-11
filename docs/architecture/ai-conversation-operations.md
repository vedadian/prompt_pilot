# AI Conversation Operations

## Purpose

This document defines the AI operations used throughout the PromptPilot conversation lifecycle.

While the conversation model defines the structure of the conversation state (`PromptContext`), this document defines how the AI transforms that state as the conversation progresses.

The operations defined here represent the behavioral contract between the application and the AI. They are independent of any specific LLM provider, prompt implementation, frontend, backend, or API.

---

# Design Principles

## Single Source of Truth

`PromptContext` is the authoritative representation of the conversation.

AI operations must derive their decisions from the current `PromptContext` rather than reconstructing state from conversation history.

## State Transitions

Each operation advances the conversation by updating `PromptContext`.

Conversation lifecycle transitions are represented exclusively through the `stage` field.

## Incremental Updates

Each operation receives the current conversation state and, when applicable, the latest question and answer.

Operations update only the information necessary for their responsibility.

---

# Question Model

```ts
type QuestionType =
  | "free_text"
  | "single_choice"
  | "single_choice_with_other"
  | "file_upload";

type Question = {
  statement: string;
  type: QuestionType;
};
```

---

# AI Operations

## `clarifyGoal`

### Purpose

Clarify the user's objective until it is sufficiently understood to begin structured information gathering.

### Input

```ts
function clarifyGoal(
  promptContext: PromptContext,
  latestQuestion: Question,
  latestAnswer: string
): {
  promptContext: PromptContext;
  question?: Question;
};
```

### Behavior

This operation:

- refines the conversation goal;
- records any information already discovered during clarification;
- determines whether the goal is sufficiently understood.

If the goal is not yet clear:

- the conversation remains in `getting_started`;
- another clarification question is returned.

Otherwise:

- `stage` is changed to `information_gathering`;
- no additional question is returned.

---

## `startInfoGathering`

### Purpose

Create the initial information gathering plan.

### Input

```ts
function startInfoGathering(
  promptContext: PromptContext
): {
  promptContext: PromptContext;
  question?: Question;
};
```

### Behavior

This operation:

- classifies the conversation;
- sets the prompt category;
- populates the initial `missing` information list.

If no additional required information exists:

- `readyForReview` is set to `true`;
- `stage` becomes `review`.

Otherwise:

- the first information gathering question is returned.

---

## `gatherInfo`

### Purpose

Update the structured conversation context after each answered information gathering question.

### Input

```ts
function gatherInfo(
  promptContext: PromptContext,
  latestQuestion: Question,
  latestAnswer: string
): {
  promptContext: PromptContext;
  question?: Question;
};
```

### Behavior

This operation:

- updates `gathered`;
- updates `missing`;
- refines the goal if necessary;
- reassesses whether additional required information exists.

If additional information is required:

- returns the next question.

Otherwise:

- sets `readyForReview` to `true`;
- changes `stage` to `review`.

---

## `reviewContext`

### Purpose

Apply user-requested corrections to the reviewed prompt context.

This operation is used both:

- while reviewing the collected information; and
- after reviewing a generated prompt.

### Input

```ts
function reviewContext(
  promptContext: PromptContext,
  latestQuestion: Question,
  latestAnswer: string
): {
  promptContext: PromptContext;
  question?: Question;
};
```

### Behavior

This operation:

- updates the structured context based on user feedback;
- reassesses whether additional required information has become necessary.

If further information is required:

- updates `missing`;
- sets `readyForReview` to `false`;
- changes `stage` to `information_gathering`;
- returns the next question.

Otherwise:

- changes `stage` to `fine_tuning`;
- returns no question.

---

## `generatePrompt`

### Purpose

Generate a prompt from the current `PromptContext`.

### Input

```ts
function generatePrompt(
  promptContext: PromptContext
): {
  promptContext: PromptContext;
  prompt: string;
};
```

### Behavior

This operation generates a prompt using the current `PromptContext`.

It does not modify the semantic contents of the context.

If the user accepts the generated prompt without requesting further changes:

- `stage` becomes `complete`.

Otherwise:

- the application returns to `reviewContext` so the requested changes can be applied to the `PromptContext` before generating a new prompt.

---

# Conversation Flow

```
[getting_started]
        │
        ▼
 (clarifyGoal)
        │
        ├──────────────► (clarifyGoal)
        │
        ▼
[information_gathering]
        │
        ▼
(startInfoGathering)
        │
        ▼
 (gatherInfo)
        │
        ├──────────────► (gatherInfo)
        │
        ▼
[review]
        │
        ▼
(reviewContext)
        │
        ├──────────────► [information_gathering]
        │
        ▼
[fine_tuning]
        │
        ▼
(generatePrompt)
        │
        ├──────────────► complete
        │
        ▼
(reviewContext)
```

---

# Relationship to the Conversation Model

This document complements `conversation-model.md`.

- The conversation model defines the conversation state.
- This document defines how AI operations transform that state throughout the conversation lifecycle.