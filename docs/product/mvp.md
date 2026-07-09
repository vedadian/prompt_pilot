# MVP Specification

## Purpose

The purpose of the MVP is to validate that users can create significantly better AI prompts through a guided conversation than by starting with a blank prompt.

The application should demonstrate:

* Strong product thinking
* Excellent user experience
* Intelligent AI-assisted conversations
* Clean, maintainable engineering

Every feature included in this document must directly contribute to that goal.

---

# Guiding Principle

PromptPilot is **not** a chatbot that follows a predefined questionnaire.

Its purpose is to progressively build a structured information model tailored to the user's goal.

The generated prompt is derived from that information model, **not** directly from the conversation transcript.

---

# Access Model

The MVP is designed around immediate use without authentication.

Users can:

* Start a conversation
* Complete a guided conversation
* Generate a prompt
* Copy the prompt
* Open the prompt in ChatGPT

Conversation history is retained only for the current browser session.

---

# Conversation Lifecycle

Every conversation progresses through the following stages.

---

## 1. Exploration

Goal

Understand what the user wants to accomplish.

Characteristics

* The user describes their goal.
* The AI asks broad follow-up questions.
* The prompt category is still unknown.
* Required information has not yet been identified.

Completion

The AI understands the user's intent well enough to classify the conversation.

---

## 2. Classification

Goal

Determine the category of the requested prompt.

Characteristics

* The conversation receives a category.
* The AI determines what information must be collected.
* The required information model is created dynamically for this conversation.

Unlike the prompt category, the required information model is **never predefined**.

Completion

The AI has identified the information required to produce a high-quality prompt.

---

## 3. Information Gathering

Goal

Collect all information required by the dynamically generated information model.

Characteristics

* Questions become increasingly specific.
* Previously collected information is reused whenever possible.
* Missing information is collected through natural conversation.
* The AI may refine, merge or replace information categories as understanding improves.

Completion

The AI determines that sufficient information has been collected.

---

## 4. Review

Goal

Confirm the collected information before prompt generation.

Characteristics

The application presents a structured summary of the collected information.

The user can:

* Confirm
* Correct
* Add information

Completion

The user approves the summary.

---

## 5. Prompt Generation

Goal

Generate a high-quality prompt.

Characteristics

The generated prompt can be:

* Copied
* Opened in ChatGPT

The conversation is complete.

---

# Initial Prompt Categories

The MVP should recognize, at minimum, the following categories.

## Writing

Examples:

* Articles
* Emails
* Documentation
* Marketing copy
* Reports

---

## Software Development

Examples:

* Code generation
* Debugging
* Refactoring
* Testing
* Architecture

---

## Learning & Education

Examples:

* Explanations
* Study plans
* Summaries
* Quizzes

---

## Business

Examples:

* Brainstorming
* Product planning
* Marketing
* Research
* Analysis

---

## Visual Creation

Examples:

* Image generation
* Logos
* Illustrations
* Posters
* Product concepts
* UI concepts

---

## Image Editing

Examples:

* Background removal
* Object replacement
* Image enhancement
* Style changes
* Inpainting

---

## Video Creation

Examples:

* AI video prompts
* Storyboards
* Scene generation
* Animation concepts

---

## Data & Analysis

Examples:

* CSV analysis
* Research
* Reports
* Data interpretation

---

## General

Fallback category when no better classification exists.

---

# Interaction Principles

The interface should always prefer structured input over free-form typing whenever practical.

## Buttons

Use for:

* Primary actions
* Yes / No decisions
* Continue
* Generate Prompt
* Copy Prompt

---

## Single Selection

Use radio buttons or segmented controls when exactly one option should be selected.

---

## Multiple Selection

Use checkboxes when multiple options may be selected.

---

## Free Text

Use only when structured input would unnecessarily restrict the user.

---

## File Upload

Offer uploads only when they can meaningfully improve the generated prompt.

Examples:

* Documents
* Images
* Specifications
* Existing content

Uploads are optional and context-dependent.

---

# MVP Completion Criteria

The MVP is complete when:

* Users can complete the full conversation without signing in.
* Conversation state is retained for the current browser session.
* Every conversation follows the defined lifecycle.
* A prompt category is identified for every conversation.
* A dynamic information model is created for every conversation.
* The required information is collected through guided conversation.
* Users review the collected information before prompt generation.
* Prompts can be generated, copied and opened in ChatGPT.
* The application provides a polished experience on desktop and mobile.

---

# Out of Scope

The following are intentionally excluded from the MVP:

* User accounts
* Authentication
* Persistent conversation history
* Multiple AI providers
* Team collaboration
* Prompt sharing
* Prompt templates
* Public profiles
* Notifications
* Analytics
* Accessibility audit
* Performance optimization
* CI/CD
* Monitoring
* Public API
* Native mobile applications
* Offline support
