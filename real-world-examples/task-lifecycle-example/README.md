# Task Lifecycle Example

This is a sanitized example from a real feature we built using the [explore-task](../procedures/explore-task.md) and [development](../procedures/development.md) procedures. It shows the complete set of documents produced during a feature's lifecycle — from discovery through delivery.

## The Feature

A school administration system where users manage invitation templates. Schools accumulated 15+ templates over two years, including invalid ones, but had no way to delete them. The backend DELETE endpoint already existed — this task added the frontend capability.

**Classification**: Simple Extension — follows established patterns, low risk. Single phase.

## The Documents

These documents were produced at different stages of the workflow:

| Document | Produced by | Purpose |
|----------|-------------|---------|
| [prd.md](prd.md) | **Explore-task** procedure, step 6 | Requirements: scope, user stories, functional requirements, API integration |
| [tasks.md](tasks.md) | **Explore-task** procedure, step 7 | Phased task breakdown with success criteria |
| [codebase-context.md](codebase-context.md) | **Explore-task** procedure, step 7 | Exploration results: file paths, reference patterns, data flow |
| [active-context.md](active-context.md) | **Development** procedure, maintained throughout | Running log: what's done, key decisions, what remains |

## What to Notice

- **The PRD defines WHAT, not HOW.** It references existing code patterns by file path but doesn't prescribe implementation details.
- **The task breakdown follows functional slices.** All 4 tasks deliver as a single phase because they're interdependent and small — consistent with the "Simple Extension" classification.
- **The codebase context is specific.** It names exact files, line numbers, and data flow. This is what enables an AI agent (or a new session of the same agent) to implement the feature without re-exploring the codebase.
- **The active context captures decisions.** The "Key Decisions" section records choices that were made during implementation and why — critical for anyone reviewing the code later.
- **Czech UI strings are preserved.** This is a Czech-language product. The PRD uses the actual UI strings that will appear in the application, not translated placeholders.
