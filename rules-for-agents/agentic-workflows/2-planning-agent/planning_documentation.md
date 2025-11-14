# Task Documentation & Context Setup Instructions

These instructions guide the documentation phase where you create the final task documentation and active context files that implementation agents will use to execute the feature.

- Purpose: Documentation creation and context setup ONLY
- Permitted: Creating tasks.md and active-context.md files, structuring implementation guidance
- Forbidden: Making implementation decisions, changing task scope, adding new requirements
- Requirement: You must create clear, actionable documentation based on the planning completed in previous phases
- Output Format: Begin with [PHASE: TASK DOCUMENTATION], then follow instructions below
- Focus: Creating implementation guidance that balances autonomy with constraint adherence

## Documentation Creation Process

**Documentation Objective**: Create complete task documentation and persistent context tracking that guides implementation agents effectively while maintaining architectural consistency across both frontend and backend.

**Required Reference**: Use the functional slice validation requirements defined in `rules-for-agents/coding/700-task-planning-agent-orchestration.md` to ensure consistency with the orchestration system.

You are to follow these steps in the current phase to achieve your objectives:

1. Tasks File Creation
2. Active Context File Setup
3. Implementation Sequencing & Dependencies
4. Quality Assurance & Completion

Below are further instructions for those steps to be followed to the letter.

### Tasks File Creation

Create a new file named `[task-code]-tasks.md` in the `documentation/development/features/[task-code]-[feature-name]/` directory.

The file must follow this exact structure:

```markdown
# [Feature Name] - Implementation Tasks

## Task Summary for Review

Unlike the rest of the document, which is for the implementation agents, the task summary is for USER review to quickly understand what will be implemented. Include:

- **High-level feature outcomes** (what the user will be able to do)
- **Key functional areas being added/changed** (specific capabilities being implemented)
- **Important business rules or constraints**
- **Any significant architectural decisions resulting from the research or task strategy steps**

EXCLUDE:

- Orchestration details (checkpoint strategies, block counts)
- Implementation methodology descriptions

## Implementation Overview

**Complexity Classification**: [Simple Extension/Complex Feature/Architectural Feature]
**Functional Slice Strategy**: [Basic Working Flow → Complete / Core Flow → Error Handling → Advanced Features / etc.]
**Total Implementation Phases**: [X phases]
**API Design Required**: [Yes/No - based on Research Phase findings]

---

## Implementation Phases

[Implementation phases as designed in previous phase]

[If API Design phase exists, MUST use this template:]

## Implementation Phase 1: API Design (OpenAPI YAML)

<critical>
**REQUIRED READING BEFORE STARTING:**
1. Read api-documentation/200-writing-api-docs.md IN FULL (all sections)
2. Read api-documentation/100-reading-api-docs.md (understand existing patterns)

**DELIVERABLE:** OpenAPI YAML files ONLY - no implementation code
</critical>

**Requirements for This Phase**:
[Specific API endpoints to be designed]
[Request/response schemas needed]
[Authentication requirements]
[Error handling specifications]

**Deliverable**:
Complete OpenAPI YAML files in `documentation/rest-api/v1/[path]/` following documented conventions.

**Validation Point**:
- All new endpoints documented with complete request/response schemas
- YAML files are syntactically valid and follow existing patterns
- No implementation code written (design only)

### Tasks:
1. Read api-documentation/200-writing-api-docs.md (entire file)
2. Review existing patterns in documentation/rest-api/
3. Design OpenAPI YAML for [list specific endpoints]
4. Validate YAML completeness

### Self-Check Criteria:
- [ ] Read complete API documentation (200-writing-api-docs.md)
- [ ] Endpoints follow directory structure = URL path convention
- [ ] Responses use standard structure (meta + data/error)
- [ ] File names follow convention (GET.yaml, POST.yaml, GET-PATCH-DELETE.yaml)
- [ ] Schemas defined in components/ and properly referenced

**STOP HERE - AWAIT VALIDATION**

---

**Feature-Specific Constraints**:

- [Select applicable coding rules from the comprehensive list below - include ALL that apply to this feature]
- [List specific patterns that must be followed for this feature]
- [List any feature-specific validation requirements]

**Available Coding Rules for Selection**:

Reference rules from `rules-for-agents/coding/`:
- **Always include**: 001-general-coding-rules.md (required for all tasks)
- **For frontend work**: 002-frontend-angular-primeng-rules.md, 600-frontend-api-calls-rules.md, 601-frontend-user-feedback-rules.md
- **For backend work**: 003-backend-essentials.md
- **For API design**: api-documentation/200-writing-api-docs.md (ENTIRE file - MANDATORY for API design phases)
- **For code quality**: 004-refactoring-rules.md

Select only the rules that are relevant to this specific feature - don't just list everything
```

### Active Context File Setup

Create a new file named `[task-code]-active-context.md` in the same directory.

The file must follow this exact structure (only show sections when they contain actual content):

```markdown
# [Task Code] - [Feature Name] Active Context

## Current Status: [Not Started/In Progress/Blocked/Complete]

**Current Phase**: [Phase Name]  
**Overall Progress**: X% complete

## Completed Work

[Only list when work is actually completed - remove this section if empty]

## Remaining Work

[List upcoming phases in order]

## Current Blockers

[Only show this section if blockers exist - remove if none]

## Deviations from Plan

[Only show this section if deviations occurred - remove if none]
```

### Implementation Agent Guidance Integration

<critical>
ABSOLUTE PROHIBITION: NO CODE IN TASK DOCUMENTATION

You are creating guidance for skilled implementation agents, NOT writing a coding tutorial. The tasks file must contain ZERO:
- Code examples (TypeScript, HTML, CSS, SQL, YAML, etc.)
- Method signatures or function definitions
- Step-by-step coding instructions
- Pseudo-code or algorithmic details
- Specific variable/class/property names
- Line-by-line implementation guides

If you find yourself writing "```typescript" or similar code blocks, STOP IMMEDIATELY - you are violating this critical requirement.

Implementation agents will:
1. Read the coding rules you reference
2. Study the pattern files you point to
3. Implement the functionality you describe
4. Validate using the criteria you provide

Your job is steps 1, 2, and 4 - NOT step 3.
</critical>

Within the tasks file, ensure each implementation phase includes feature-specific guidance that focuses on delivering working functionality. Generic agent behaviors are covered in implementation agent instructions.

**API Design Phase Special Requirements**:

If Research Phase identified new endpoints, the tasks file MUST include:
- Instruction to read api-documentation/200-writing-api-docs.md (entire file)
- List of specific endpoints to design
- Clear deliverable: OpenAPI YAML files only (no implementation code)

**Coding Rules Selection Guidelines**:

When creating the Feature-Specific Constraints section, you MUST:

- **Think critically about each rule** - select what's actually needed for this specific feature
- **Always include 001-general-coding-rules.md** as it's required for any coding task
- **Consider the full stack**: Include both frontend and backend rules if the feature spans both layers
- **For API Design phases**: api-documentation/200-writing-api-docs.md is MANDATORY (entire file)

### Implementation Sequencing & Dependencies

When phases have dependencies, focus on delivering complete working slices:

**Complete Functional Slices** (Recommended):

- Each phase delivers fully working end-to-end functionality (frontend + backend)
- Include all necessary elements (API endpoints, error handling, basic styling) for the slice to work
- Example: "Phase 1: Complete basic user authentication flow with API endpoints, JWT handling, and error messages"

**Incremental Enhancement Approach** (When full integration not feasible):

- Earlier phases deliver minimal but complete working functionality
- Later phases enhance and expand the working functionality
- Ensure each phase can be independently tested and validated
- Example: "Phase 1: Basic API design (OpenAPI) - Phase 2: Backend implementation - Phase 3: Frontend integration"

**Dependency Documentation**:

- Clearly state what working functionality each phase builds upon
- Note what user-testable capabilities each phase adds
- Specify validation criteria that verify complete working functionality

**Research Guidance**:

- Specific files to research with focus areas
- Patterns to understand and follow exactly
- Coding rules that apply to the implementation

**Implementation Scope**:

- Clear boundaries of what should be implemented
- Expected deliverable at block completion
- Self-validation criteria

**Constraint References**:

- Specific coding rules to follow (by name, not content reproduction)
- Architectural patterns to maintain

### Quality Assurance & Completion

Before completing this phase, verify:

**Tasks File Quality**:

- All implementation phases have clear working functionality deliverables
- Research guidance is specific and actionable
- Validation criteria focus on user-testable outcomes
- Coding rules are referenced appropriately

**Implementation Agent Readiness**:

- Agents have complete context for delivering working functionality that could be gathered from the PRD document
- All research directions relevant to functional slices were passed along to the implementation agent
- Validation criteria are executable and focus on working user functionality
- Each phase delivers complete testable functionality
- **Critical**: All phases are complete and cover all requirements from the PRD document

## Documentation Completion Signal

When documentation is complete, end with:

**[TASK DOCUMENTATION COMPLETE - READY FOR IMPLEMENTATION]**

Include a final summary:

- **Tasks File**: [path/filename]
- **Active Context File**: [path/filename]
- **Implementation Strategy**: [Brief summary]
- **First Implementation Agent**: Should start with [Phase Name]

**Phase 3 Output**: Complete `[task-code]-tasks.md` and `[task-code]-active-context.md` files ready for implementation agents
