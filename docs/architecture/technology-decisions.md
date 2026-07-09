# Engineering Principles

The technology decisions in this document are guided by a small set of engineering principles that define how PromptPilot is designed and developed. These principles take precedence over convenience and influence every architectural decision made throughout the project.

## Prefer Standards over Frameworks

Whenever practical, favor open standards, well-defined interfaces, and language-native capabilities over framework-specific solutions. This minimizes vendor lock-in and keeps the application portable and maintainable.

## Own the Critical Parts of the System

Core application concerns—such as authentication, conversation orchestration, and AI interaction—should remain under the application's control whenever feasible. External services should enhance the application, not define its behavior.

## Avoid Unnecessary Abstractions

Abstractions should solve existing problems rather than anticipated ones. Every additional layer introduces complexity and should have a clear, demonstrable purpose.

## Introduce Dependencies Deliberately

External libraries and services should only be introduced when they provide meaningful value that outweighs their maintenance and operational costs. Convenience alone is not sufficient justification for adding a dependency.

## Depend on Stable Interfaces

PromptPilot should depend on stable, well-defined interfaces rather than convenience frameworks or provider-specific implementations. The application's business logic should remain focused on the problem domain, while infrastructure concerns remain isolated behind clear boundaries.


# Repository Organization

## Decision

PromptPilot will use a **monorepo**, with all project artifacts maintained in a single Git repository.

The repository will contain the frontend, backend, documentation, and any future project components. Each application will remain independent and may use the technology stack, tooling, and package manager best suited to its purpose.

## Rationale

A monorepo provides a single source of truth for the entire project, making PromptPilot easier to develop, maintain, and showcase as a complete portfolio piece.

Keeping all related artifacts together allows the project to evolve cohesively while avoiding the overhead of managing multiple repositories.

At the same time, treating each application as an independent project preserves the flexibility to choose the most appropriate technologies for each layer. For example, the frontend and backend may use different languages, frameworks, runtimes, and package managers without imposing unnecessary constraints on either.

## Consequences

### Benefits

- Single repository for the entire project
- Simplified versioning and project management
- Easier onboarding and development
- Complete portfolio project in one place
- Technology-agnostic application boundaries

### Trade-offs

- Requires clear repository organization
- Shared tooling should only be introduced where it provides real value
- Independent applications may require separate setup and build processes

# Backend Technology Stack

## Decision

The PromptPilot backend will be implemented using **PHP** with the **Slim Framework**.

Additional libraries and components (e.g. database abstraction, authentication, AI SDKs, logging, caching) will be evaluated and introduced as the project evolves. They are intentionally outside the scope of this decision.

## Rationale

The primary architectural constraint for PromptPilot is ensuring that the application is reliably accessible to users in Iran while keeping operational costs close to zero.

Although serverless platforms such as Cloudflare Workers provide an excellent developer experience and generous free tiers, their accessibility from within Iran cannot be relied upon due to unpredictable network restrictions.

PHP applications can be deployed to virtually any low-cost shared hosting provider using cPanel and MySQL, providing a mature, inexpensive, and broadly compatible hosting environment with minimal operational overhead.

The Slim Framework was selected because it provides a lightweight and flexible foundation without imposing unnecessary structure. It allows the application's architecture to grow organically while remaining framework-agnostic where practical.

## Consequences

### Benefits

- Broad hosting compatibility
- Very low deployment cost
- Reliable deployment on commodity shared hosting
- Lightweight framework with minimal overhead
- Freedom to introduce additional libraries only when needed

### Trade-offs

- Foregoes the serverless deployment model offered by platforms such as Cloudflare Workers
- Requires management of a traditional web server environment
- Some modern edge-native capabilities are not available by default


# Frontend Technology Stack

## Decision

The PromptPilot frontend will be built using:

- Vite
- React
- TypeScript

The user interface will use:

- daisyUI
- Heroicons

Additional libraries (e.g. routing, state management, data fetching, form handling, testing) will be selected as the project evolves and are intentionally outside the scope of this decision.

## Rationale

The frontend should provide a modern, fast, and maintainable development experience while remaining lightweight and flexible.

Vite offers a fast development server and efficient production builds with minimal configuration.

React provides a mature component model well suited to building an interactive conversational interface.

TypeScript improves maintainability through static typing and enables safer refactoring as the project grows.

daisyUI supplies a consistent set of accessible UI components built on Tailwind CSS, allowing rapid interface development while maintaining visual consistency.

Heroicons complement the design with a clean and cohesive icon set.

## Consequences

### Benefits

- Fast development workflow
- Strong TypeScript support
- Large ecosystem and community
- Component-based architecture
- Consistent UI with minimal custom styling
- Excellent developer experience

### Trade-offs

- Relies on the React ecosystem
- Tailwind CSS and daisyUI introduce utility-first styling conventions
- Additional frontend libraries will need to be evaluated as requirements emerge


# Persistence Strategy

## Decision

PromptPilot will use:

- MySQL for structured application data.
- The local filesystem for user-uploaded files.

Cloud-based object storage is intentionally deferred until it becomes necessary.

## Rationale

The application requires persistent storage for conversations, users, generated prompts, and other structured data. MySQL is a mature, widely supported relational database available on virtually all shared hosting providers and integrates naturally with the chosen backend stack.

User-uploaded files are expected to be an optional feature that enriches prompt generation. Since the application will initially be deployed on traditional shared hosting, storing uploads on the local filesystem provides a simple and cost-effective solution without introducing additional infrastructure.

Should storage requirements grow in the future, the filesystem layer can be replaced with an object storage solution while preserving the application's overall architecture.

## Consequences

### Benefits

- Broad hosting compatibility
- No additional infrastructure required
- Very low operating cost
- Simple backup and deployment strategy
- Straightforward integration with the backend

### Trade-offs

- Uploaded files are tied to a single application instance
- Local filesystem storage is less suitable for horizontally scaled deployments
- Future migration to object storage may be required if storage requirements increase


# Authentication Strategy

## Decision

PromptPilot will use a custom authentication implementation built into the backend.

Authentication will be based on standard web authentication practices without relying on third-party identity providers or authentication-as-a-service platforms.

The implementation details (password hashing, session management, remember-me functionality, password reset, etc.) will be determined during implementation.

## Rationale

The primary requirement is to provide a reliable authentication experience for users in Iran.

Third-party authentication providers may become inaccessible due to network restrictions, introducing an external dependency that directly impacts the usability of the application.

Implementing authentication within the application eliminates this dependency while providing complete control over the authentication flow and user management.

## Consequences

### Benefits

- No dependency on third-party authentication services
- Reliable authentication independent of external providers
- Full control over user accounts and authentication flows
- Broad compatibility with traditional shared hosting

### Trade-offs

- Authentication features must be implemented and maintained within the application
- Responsibility for security best practices rests entirely with the application


# AI Provider Strategy

## Decision

PromptPilot will communicate directly with LLM provider APIs through an internal provider abstraction.

The application will not depend on AI orchestration frameworks or intermediary services. New providers can be introduced by implementing the internal provider interface.

The initial provider will be selected during implementation and does not form part of this architectural decision.

## Rationale

PromptPilot's core responsibility is orchestrating conversations and generating prompts. This logic should remain independent of any specific LLM provider or orchestration framework.

Using a provider abstraction keeps the application's business logic isolated from provider-specific APIs while avoiding unnecessary dependencies on third-party AI frameworks.

Direct integration also provides complete control over prompts, request flow, error handling, and future provider selection.

## Consequences

### Benefits

- Business logic remains provider-independent
- New providers can be added with minimal impact
- No dependency on AI orchestration frameworks
- Full control over request lifecycle and prompt construction
- Reduced external dependencies

### Trade-offs

- Provider adapters must be implemented and maintained
- Common functionality across providers must be handled by the application


# Hosting Strategy

## Decision

PromptPilot will be deployed to a traditional shared hosting environment supporting PHP and MySQL.

The frontend will be built as static assets and served by the same hosting environment.

The specific hosting provider is intentionally left undecided and may be selected based on cost, reliability, and accessibility.

## Rationale

The selected hosting strategy aligns with the project's primary requirements:

- Reliable accessibility for users in Iran
- Very low operational cost
- Broad compatibility with the chosen backend technology
- Minimal infrastructure management

Using a single hosting environment simplifies deployment and maintenance while avoiding unnecessary operational complexity.

## Consequences

### Benefits

- Low hosting cost
- Simple deployment model
- Broad hosting compatibility
- Minimal operational overhead

### Trade-offs

- Limited scalability compared to cloud-native platforms
- Deployment is tied to a traditional hosting model

---

# Out of Scope

This issue intentionally focuses on architectural decisions that are foundational to the project and costly to change later.

Implementation-specific technologies and libraries—such as ORMs, routing libraries, state management, testing frameworks, logging, caching, and similar tooling—will be evaluated and introduced only when they address a concrete project need.

Deferring these decisions avoids premature optimization, keeps the technology stack lean, and allows the architecture to evolve organically based on the project's actual requirements rather than anticipated ones.
