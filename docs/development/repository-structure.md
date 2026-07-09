# Repository Structure

## Overview

The PromptPilot repository is organized around the project's primary concerns: the frontend application, the backend application, and the project documentation.

```text
promptpilot/
│
├── front/
├── back/
├── docs/
│
├── .gitignore
├── LICENSE
└── README.md
```

Each top-level directory has a single, well-defined responsibility.

## Repository Layout

### `front/`

Contains the React frontend application and all frontend-specific source code, configuration, dependencies, and build artifacts.

### `back/`

Contains the PHP backend application and all backend-specific source code, configuration, dependencies, and runtime assets.

### `docs/`

Contains all project documentation.

Documentation is organized by concern:

- `architecture/` — Technical and architectural documentation, including technology decisions, system design, API documentation, and other engineering artifacts.
- `product/` — Product-oriented documentation, including the project vision, MVP definition, roadmap, and user experience artifacts.
- `development/` — Development-related documentation, including repository structure, development workflow, setup guides, and contributor documentation.

This organization is intended to provide a clear separation between product, architecture, and development concerns while remaining simple enough to evolve naturally as the project grows.

### Root Files

#### `.gitignore`

Configured to ignore generated files, dependencies, build outputs, IDE configuration, and other local artifacts produced by both the frontend and backend.

#### `LICENSE`

The project is released under the MIT License.

#### `README.md`

Provides a high-level overview of PromptPilot, setup instructions, and links to the project documentation.

---

### Shared Code

At this stage, the project does not contain any shared code between the frontend and backend.

Should shared functionality emerge during development, an appropriate location will be introduced based on the nature of that code. Until then, each application remains self-contained.

---

# Out of Scope

This document defines the high-level organization of the repository only.

The internal structure of the frontend and backend applications, framework-specific directory layouts, build artifacts, development tooling, and application configuration are intentionally left to their respective technologies and will be addressed during project initialization.

Likewise, additional top-level directories (such as shared libraries, scripts, infrastructure, or tooling) will only be introduced when they address a demonstrated project need.

This approach keeps the repository structure aligned with the project's engineering principles of avoiding unnecessary abstractions and introducing structure incrementally.

# Rationale

The repository structure intentionally reflects the architecture of the project rather than the implementation technologies.

PromptPilot consists of two independent applications—a frontend and a backend—that are developed and versioned together within a single repository. Keeping each application self-contained simplifies development while avoiding unnecessary coupling between their respective toolchains.

No additional top-level directories are introduced until a clear need emerges. Shared code, scripts, libraries, or infrastructure should only receive dedicated locations when they provide demonstrable value to the project.

This approach follows the project's engineering principles of avoiding unnecessary abstractions and introducing structure only when it solves an existing problem.

---

# Future Evolution

The repository structure is expected to evolve alongside the project.

New top-level directories should only be introduced when they represent new architectural concerns rather than anticipated future requirements.
