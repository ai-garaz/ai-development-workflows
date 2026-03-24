# Explore Task — Feature Discovery Procedure

Gather all project and codebase context for a development task, produce a task analysis document, PRD, and implementation plan. Checkpoint with the user at key decision points before proceeding.

## Scope of This Procedure

These steps define the baseline for every task, regardless of complexity. A trivial task produces trivial artifacts at each step; a complex task produces thorough ones. If the task requires additional research rounds, deeper pattern analysis, or investigation paths not listed here — do that. The steps below are a floor, not a ceiling.

## Trigger

- User provides a task code or issue URL and wants to start development
- "let's work on [task]", "let's explore [task]", "plan the feature"

---

## Workflow

### 1. Gather Issue Context

Sequential, before anything else. Everything depends on understanding the issue content.

Fetch the issue from your project management tool. Extract:
- Domain problem, affected repos/systems, what's done vs. remaining
- Domain terms and keywords (for search prompts)
- Whether there are attached images/mockups
- Comments, especially if they mention notes from meetings
- **Related issues** referenced in description or comments — fetch those too, even if the user only gave one

### 2. Review Visual Specifications

Sequential, before spawning agents. Mockups often contain the most precise requirements (API response structures, exact UI layouts, icon choices). Agent prompts cannot be written well without understanding the visual spec.

### 3. Research

Spawn focused research agents. Prompts are informed by steps 1-2. Give each agent **specific paths and search keywords** — sub-agents don't have your CLAUDE.md context.

**How to structure agents**: Identify the distinct research domains this task touches and spawn one focused agent per domain. A narrow agent that exhausts its domain produces better results than a broad agent spread across many concerns.

#### Research Domains

Cover all of the following that are relevant to the task:

**Project context**: Task plan data (estimates, deadlines, phase, priority). Meeting decisions and clarifications. Domain knowledge the feature depends on. Past work on related tasks. Existing analysis if any.

**Codebase structure**: Full data flow trace: API endpoint → service → schema/types → component → template. Existing patterns for similar functionality. Permissions and guards that gate the feature area.

**Data model** (when the task touches persistent data): Database schema — tables, columns, FKs, constraints. Denormalized fields and sync dependencies. Shared records referenced by multiple entities.

**Reference pattern extraction** (when the task replicates an existing codebase pattern): This is critical for tasks that follow an existing pattern. Spawn a dedicated agent to extract the COMPLETE reference pattern — every state variable, every method, every component input/output, every service injection, every form control. This is not a summary — it is an exhaustive extraction that directly informs the PRD and plan.

#### Research Discipline

Learned lessons where the naive approach gives worse results:

1. **Meeting minutes are an index, not a source.** Use minutes to identify relevant meetings, then verify against the transcript directly. Summaries are lossy.
2. **Search by domain terms, not task codes.** Meetings predate issue creation. Searching "TASK-882" finds nothing; searching the domain term in the native language finds the discussion.
3. **Sweep knowledge broadly.** The issue assumes domain rules it doesn't cite. A feature touching one domain area may be affected by rules from adjacent areas the issue never mentions.
4. **Trace full data flow for code, don't just grep.** Finding the component isn't enough. Trace upstream to the service, schema, and API endpoint. Trace downstream to the template.
5. **Fetch all related issues.** The user gives one code, but the description may reference others — the backend spec, the predecessor with rejected approaches, the parent epic with scope decisions.

### 4. Present Findings and Clarify

After all research agents return, synthesize and present to the user BEFORE creating formal documents.

**What to present**: Summary of findings, key facts that shape the feature, the reference pattern that will be followed (if applicable).

**What to flag**: Contradictions between sources. Assumptions the spec makes that the codebase doesn't clearly support. Ambiguous terms used differently across sources. Open questions about WHAT should happen.

**Clarifying questions with suggestions**: For each uncertainty, propose how you would handle it — e.g., "The spec doesn't say whether users should be able to edit this field. I'd assume yes. Should I proceed with that?" This makes it easy for the user to approve or redirect.

Wait for the user's response before proceeding.

### 5. Create Task Analysis

After clarifications are resolved, create a formal analysis document with sections for:

1. Issue specs, API structures, visual design specs, BE/FE status
2. Plan data — estimates, deadlines, contract info
3. Meeting context — decisions, clarifications, rejected alternatives
4. Related completed work — predecessor tasks, PRs deployed
5. Domain knowledge — relevant knowledge base entries
6. Current system behavior — what exists before this task
7. Timeline and urgency
8. Technical stack context — only what's relevant
9. Image references — local paths to mockup images
10. Clarifications and resolved questions

**Approval gate**: Present the task analysis to the operator. This is the formal checkpoint — the analysis must be approved before proceeding to PRD creation.

### 6. Create PRD

After the task analysis is approved, create the PRD.

- Set up a feature branch and worktree for the work
- Use a PRD template as a starting point (the template's questions set the depth expectation)
- Calibrate against existing PRDs for similar features
- Save to the feature docs directory inside the worktree

### 7. Create Implementation Plan

From the task analysis and PRD, create a phased implementation plan.

- Break work into **functional slices** (see Planning Principles below), not architectural layers
- Classify complexity to determine the number of phases
- Each phase must include: working end-to-end functionality, a clear way for the user to test it, specific success criteria
- **Identify applicable coding rules** — the implementation agent works in a separate context and will not independently discover which rules matter. List them explicitly with a brief reason for each.
- Save both the task breakdown and a codebase context document (file paths, data flow, patterns, applicable rules)

### 8. Session Tracking

Update the daily log with a summary. Create a task file if the work is significant enough for standalone tracking.

**Checkpoint**: "Analysis, PRD, and implementation plan complete. Ready to begin implementation?"

---

## Planning Principles

These are thinking habits that measurably improve outcomes. Apply when creating the PRD (step 6) and implementation plan (step 7).

### Define the Problem, Not the Solution

Focus on the required outcome, not on prescribing technical solutions or UI component choices. Those decisions belong to implementation time, when you have full codebase context.

### Assume Incomplete Input

The user has not provided everything needed. Your job is to identify gaps and ask about them. Organize questions by category:

- **Scope**: Feature boundaries, what is included vs excluded, which user roles are affected.
- **User Stories**: Specific user interactions, alternative flows, edge cases.
- **Requirements**: Functional behavior details, validation rules, constraints.
- **References**: Why specific files/services were mentioned, whether to extend or use as reference.
- **Risks**: Potential challenges, concurrent editing conflicts, data migration concerns.

Limit to 3-5 questions for small features, 5-8 for medium, 8-12 for large. If the user does not answer a question, treat it as not important and omit it entirely from all subsequent documentation.

### Do Not Extrapolate

Only include requirements, risks, and functionality that were explicitly stated or confirmed. Do not add features the user did not ask for. Do not invent concerns that were not raised.

### Reference Patterns, Do Not Dissect Them

When told to follow an existing pattern, reference it by exact file path. Do not elaborate on its internals in planning documents. The pattern is for implementation time, not for planning to dissect.

### Functional Slices, Not Layers

Break work down by functional slices that deliver testable end-to-end functionality, not by architectural layers.

**Do not do this:**
- Phase 1: Define all types
- Phase 2: Implement all logic
- Phase 3: Build all UI
- Phase 4: Connect everything

**Do this instead:**
- Phase 1: Minimal working end-to-end flow
- Phase 2: Add error handling and edge cases
- Phase 3: Add advanced features and optimization

Each phase must include: working end-to-end functionality (however minimal), a clear way for the user to test it, specific success criteria, and continuity with the previous phase.

A good phase deliverable: "User can send a message and see a response." A bad phase deliverable: "TypeScript interfaces and types for the new provider." The first is testable. The second is not.

### Complexity Classification

Classify features by risk and dependency, not size.

| Classification | Characteristics | Slice Strategy |
|---|---|---|
| **Simple Extension** | Follows established patterns, low risk | Basic Working Flow → Complete (1 phase) |
| **Complex Feature** | New functionality, multiple services/components | Core Flow → Error Handling → Advanced (3 phases) |
| **Architectural Feature** | New patterns, significant system changes | Minimal Integration → Core Logic → Advanced → Complete (4 phases) |

### Cross-Context Explicitness

When planning happens in one context and implementation in another (sub-agents, next session, compressed context), be explicit. Every requirement must be transferred to task documentation. Missing requirements equal missing implementation.

### Scope Summary for Architectural Features

For features classified as Architectural (highest complexity), produce a brief written scope summary before implementation begins: what is in scope, what is out, key requirements, patterns to follow. This gets reviewed before coding starts.

---

## Planning Anti-Patterns

Failure modes learned from experience. These have caused real problems during planning.

**Do not break work into architectural layers.** Phase 1: types, Phase 2: logic, Phase 3: UI produces untestable intermediate states. Break into functional slices instead.

**Do not extrapolate requirements.** If the user did not state it and did not confirm it, do not include it. Adding assumed requirements leads to scope creep and features that don't match what was needed.

**Do not dissect reference patterns.** When told "follow the pattern in X service", reference the file path and move on. Elaborating on the pattern's internals adds noise and risks misrepresentation.
