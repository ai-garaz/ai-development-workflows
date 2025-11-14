# Agent Guide

## Required Reading (in order)

1. **[README.md](./README.md)** — Tech stack, setup instructions, project structure, deployment
2. **[documentation/about-project.md](./documentation/about-project.md)** — Project vision, roadmap, detailed specifications, V1 scope
3. **[rules-for-agents/coding/](./rules-for-agents/coding/)** — Coding standards (see below)

## Agentic Workflows

This project uses structured workflows for feature development. When asked to work in a specific mode, start by reading the corresponding prompt file:

### PRD Mode (Product Requirements)
- **When to use**: When asked to "create a PRD" or "document feature requirements"
- **Entry point**: Read `rules-for-agents/agentic-workflows/1-prd-agent/prd_prompt.md`
- **Purpose**: Transform feature requests into structured Product Requirements Documents
- **Output**: PRD document in `documentation/development/features/[task-code]-[feature-name]/`

### Planning Mode
- **When to use**: When asked to "plan a feature", "create implementation tasks", or "break down the PRD"
- **Entry point**: Read `rules-for-agents/agentic-workflows/2-planning-agent/planning_prompt.md`
- **Purpose**: Research codebase, analyze patterns, create implementation tasks with functional slices
- **Output**: Tasks file and active context tracking in feature documentation folder

### Builder Mode
- **When to use**: When asked to "implement a feature", or "build a feature", or when implementing features, fixing bugs, or handling feedback
- **Entry point**: Read `rules-for-agents/agentic-workflows/3-builder-agent/builder_prompt.md`
- **Purpose**: Precise code implementation following established patterns
- **Modes**: Coding, Feedback Implementation, Bug Fixing, Documentation

**Example Usage**:
- User: "Create a PRD for user authentication" → Enter PRD Mode
- User: "Plan the authentication feature from the PRD" → Enter Planning Mode
- User: "Implement phase 1 of the authentication tasks" → Enter Builder Mode (Coding)
- User: "Let's build TASK-123" → Enter Builder Mode (Coding)
- User: "build next phase in TASK-123" → Enter Builder Mode (Coding)

## Coding Rules

All rules are in `rules-for-agents/coding/`:

- **001-general-coding-rules.md** — Core development principles, file organization, naming conventions
- **002-frontend-angular-primeng-rules.md** — Angular + PrimeNG patterns, component structure, state management
- **003-backend-essentials.md** — Fastify API patterns, error handling, database conventions
- **004-refactoring-rules.md** — Code quality guidelines, refactoring approach
- **600-frontend-api-calls-rules.md** — API integration patterns, error handling, loading states
- **601-frontend-user-feedback-rules.md** — Toast notifications, user messaging, feedback UX

**CRITICAL:** whenever I tell you to fix something that contradicts any of your given rules, assume they are wrong and need fixing and propose the fix once we implement the solution
