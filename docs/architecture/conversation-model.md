# Conversation Model

This document defines the MVP conversation model used to guide users from an initial goal to a generated prompt.

The model exists to keep PromptPilot from behaving like a generic chatbot. The conversation may feel natural to the user, but the application is progressively building structured prompt context. The final prompt is generated from that context, not directly from the raw conversation transcript.

---

## Decision

The MVP uses a stable `PromptContext` shape for every conversation.

Prompt-specific details are represented as lists of information pieces instead of category-specific schemas. Categories guide the AI's reasoning, but they do not change the structure of the model.

```ts
type PromptCategory =
  | "writing"
  | "software_development"
  | "learning_education"
  | "business"
  | "visual_creation"
  | "image_editing"
  | "video_creation"
  | "data_analysis"
  | "general";

type ConversationStage =
  | "getting_started"
  | "information_gathering"
  | "review"
  | "fine_tuning"
  | "complete";

type IncompleteInformationPiece = {
  tags: string[];
  question: string;
};

type InformationPiece = IncompleteInformationPiece & {
  answer: string;
};

type PromptContext = {
  id: string;
  stage: ConversationStage;
  category: PromptCategory | null;
  goal: string;
  gathered: InformationPiece[];
  missing: IncompleteInformationPiece[];
  readyForReview: boolean;
};
```

---

## Field Semantics

### `id`

Unique identifier for the local conversation.

For the MVP, this identifier only needs to support the current browser session. Persistent conversation identity is outside the MVP.

### `stage`

Represents the current lifecycle state of the conversation.

- `getting_started` - The user is describing the goal, and the application does not yet have enough information to classify the prompt task.
- `information_gathering` - The task has been classified, and the application is collecting required missing information.
- `review` - The application has enough information to generate a useful prompt, and the user is reviewing the collected context.
- `fine_tuning` - A prompt has been generated, and the user is refining the result.
- `complete` - The user has finished the prompt generation flow.

### `category`

The recognized prompt category.

The category is `null` while the conversation is still in `getting_started`. Once the task can be classified, the category should be set to one of the supported `PromptCategory` values.

The category helps the AI decide what information is likely to be required, but it does not introduce a category-specific data model in the MVP.

### `goal`

The user's primary objective.

The goal is first-class because the final prompt must be generated from the structured model rather than relying on the transcript. The goal may be refined as the conversation clarifies what the user actually wants.

### `gathered`

Information that has been collected and can be used during prompt generation.

Each item records the question that produced the information, the user's answer, and one or more tags describing what the answer contributes to the prompt context.

### `missing`

Required information that has not yet been collected.

`missing` contains required information only. Optional improvements should not block review. They may be offered during fine-tuning after the first useful prompt has been generated.

### `readyForReview`

Indicates whether the application can move to the review stage.

This should be `true` when the required missing information has been collected and the gathered context is sufficient to generate a specific, useful prompt.

---

## Lifecycle

The model stages are implementation states. They map to the product lifecycle as follows:

- Product `Exploration` maps to `getting_started`.
- Product `Classification` is a transition where the first structured `PromptContext` is created; it is not stored as a long-running stage.
- Product `Information Gathering` maps to `information_gathering`.
- Product `Review` maps to `review`.
- Product `Prompt Generation` moves the conversation into `fine_tuning`, then `complete` when the user is done.

### 1. Getting Started

The user starts by describing what they want to accomplish.

During this stage, the AI should focus on understanding intent. It should not attempt to collect every possible detail before classification.

The conversation remains in `getting_started` until the AI can classify the prompt generation task.

### 2. Classification

Once the task can be classified, the AI creates the first structured `PromptContext`.

At this point, the AI should:

- Set `category`.
- Set or refine `goal`.
- Extract any already gathered information from the conversation.
- Create the initial `missing` list.
- Set `stage` to `information_gathering`.
- Set `readyForReview` to `false`.

This is the first point where required missing information should be planned.

### 3. Information Gathering

The application asks for missing information.

By default, it should ask one missing information piece at a time. Related questions may be grouped only when they are easy for the user to answer together.

After each user answer, the AI reassesses the full `PromptContext`. It may:

- Move answered information from `missing` to `gathered`.
- Revise gathered information when the user's answer clarifies earlier context.
- Add newly discovered required missing information.
- Remove missing information that is no longer required.
- Merge duplicate or overlapping information pieces.
- Set `readyForReview` to `true` when enough information has been collected.

The missing information plan is therefore dynamic. It is created after classification, but it is not frozen.

### 4. Review

When `readyForReview` is `true`, the application presents the gathered context to the user for confirmation.

The review screen should be based on `goal`, `category`, and `gathered`. The raw transcript should not be the primary review surface.

If the user corrects or adds information during review, the application updates `gathered` and asks the AI to reassess whether any new required information is missing before prompt generation.

### 5. Fine-Tuning

After the first prompt is generated, the user may refine it.

Fine-tuning is where optional improvements belong. The application may ask about tone, format, level of detail, or other refinements if they would improve the generated prompt, but those questions should not prevent the first review-ready prompt from being produced.

### 6. Complete

The conversation is complete when the user is done with the generated prompt.

For the MVP, completed conversations are retained only for the current browser session.

---

## Required Information

A missing information piece is required only if the final prompt would likely be vague, misleading, or unusable without it.

The AI should avoid treating nice-to-have details as required. The MVP should usually collect two to five missing pieces before review. More questions are acceptable when the user's goal is complex or too under-specified to produce a useful prompt.

Examples of potentially required information:

- The audience for a writing task.
- The desired outcome of an email, proposal, or plan.
- The source material to summarize or transform.
- The target platform or format for visual or video generation.
- Constraints that materially affect the result.
- The user's current skill level for a learning task.

Examples of usually optional information:

- Preferred tone, unless tone is central to the task.
- Formatting preferences, unless the output format is the task.
- Extra polish details that would improve but not define the result.

---

## Example

```json
{
  "id": "local-001",
  "stage": "information_gathering",
  "category": "writing",
  "goal": "Write a follow-up email to a potential client",
  "gathered": [
    {
      "tags": ["audience", "recipient"],
      "question": "Who is the email for?",
      "answer": "A potential client I spoke with yesterday"
    },
    {
      "tags": ["outcome"],
      "question": "What should the email accomplish?",
      "answer": "Schedule a second meeting"
    }
  ],
  "missing": [
    {
      "tags": ["offer", "context"],
      "question": "What service or offer should the follow-up focus on?"
    }
  ],
  "readyForReview": false
}
```

After the user answers the missing question, the AI may move the conversation to review:

```json
{
  "id": "local-001",
  "stage": "review",
  "category": "writing",
  "goal": "Write a follow-up email to a potential client",
  "gathered": [
    {
      "tags": ["audience", "recipient"],
      "question": "Who is the email for?",
      "answer": "A potential client I spoke with yesterday"
    },
    {
      "tags": ["outcome"],
      "question": "What should the email accomplish?",
      "answer": "Schedule a second meeting"
    },
    {
      "tags": ["offer", "context"],
      "question": "What service or offer should the follow-up focus on?",
      "answer": "A website redesign for their consulting business"
    }
  ],
  "missing": [],
  "readyForReview": true
}
```

---

## MVP Boundaries

The MVP conversation model does not include:

- Category-specific schemas.
- Persistent user history.
- User accounts.
- Multi-provider AI behavior.
- Prompt templates.
- Optional information scoring.
- Analytics or evaluation metrics.

These can be introduced later if the product needs them.
