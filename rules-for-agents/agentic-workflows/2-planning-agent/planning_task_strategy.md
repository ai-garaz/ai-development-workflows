# Task Planning & Checkpoint Strategy Instructions

These instructions guide the task planning phase where you break down the feature into implementation blocks with strategic checkpoints based on foundational vs additive work classification.

- Purpose: Strategic task breakdown with smart checkpoint placement ONLY
- Permitted: Creating task blocks, defining checkpoints, planning implementation sequence
- Forbidden: Writing detailed implementation steps, creating final documentation
- Requirement: You must create appropriately-scoped tasks with validation points that prevent architectural failures
- Output Format: Begin with [PHASE: TASK PLANNING], then follow instructions below
- Focus: Balancing implementation efficiency with validation safety using foundational/additive work principles

## Task Planning Process

**Planning Objective**: Create implementation blocks that deliver testable functionality in working slices, ensuring users can validate progress at each checkpoint. For full-stack features, enforce the mandatory API-first workflow when new endpoints are required.

**Required Reference**: Use the complexity classification and functional slice principles defined in `rules-for-agents/coding/700-task-planning-agent-orchestration.md` to guide your task breakdown strategy.

**Context**: This is a full-stack project with mandatory sequencing for new API endpoints.

<critical>
**IF RESEARCH PHASE IDENTIFIED NEW ENDPOINTS:**

Mandatory 3-phase minimum (cannot skip or reorder):
1. **Phase 1: API Design** - Create OpenAPI YAML specifications
2. **Phase 2: Backend Implementation** - Implement Fastify routes matching the spec
3. **Phase 3: Frontend Implementation** - Consume the implemented API

You may add additional phases for complexity, but these three must exist in this order.

**IF NO NEW ENDPOINTS:**
Flexible phase structure - frontend and backend may be developed in parallel.
</critical>

You are to follow these steps in the current phase to achieve your objectives:

1. Complexity Assessment & Checkpoint Strategy Selection
2. Implementation Block Creation
3. Task Boundary Definition
4. Validation Point Design
5. Planning Summary & Completion

Below are further instructions for those steps to be followed to the letter.

### Complexity Assessment & Checkpoint Strategy Selection

Based on your research findings, determine the appropriate functional slice strategy using the classifications defined in `rules-for-agents/coding/700-task-planning-agent-orchestration.md`:

<critical>
**Check Research Phase API Requirements:**

**IF NEW ENDPOINTS REQUIRED:**
- Minimum 3 phases: API Design → Backend → Frontend (cannot skip or reorder)
- May add more phases for complexity (e.g., split backend into Core + Advanced)
- Example: Phase 1: API Design | Phase 2: Backend Core | Phase 3: Frontend Basic | Phase 4: Backend Advanced | Phase 5: Frontend Complete

**IF NO NEW ENDPOINTS:**
- Flexible phase structure (1-3 phases based on complexity)
- Frontend and backend may be developed in parallel
- Example: Phase 1: Core Flow | Phase 2: Advanced Features

API requirements override other considerations - if new endpoints exist, API Design MUST be Phase 1.
</critical>

### Implementation Block Creation

Create implementation blocks following this structure:

**Block Template**:

```markdown
## Implementation Phase X: [Phase Name]

**Requirements for This Phase**:

- [Specific PRD requirements that must be implemented in this phase]
- [Business rules that apply to this phase's scope]
- [Data requirements for this phase]
- [Error handling requirements for this phase]

**Deliverable**:

- [Specific, testable working functionality user can interact with]
- [How user can test and verify this phase works]

**Validation Point**:

- [What working functionality should be demonstrable]
- [Specific user actions that should work]
- [Evidence that approach is sound for next phase]

### Tasks:

[List of related tasks for this functional slice]

### Self-Check Criteria:

[Specific validation steps agents should perform to verify working functionality]

**STOP HERE - AWAIT VALIDATION**
```

**Functional Slice Focus**: Reference `rules-for-agents/coding/700-task-planning-agent-orchestration.md` for complete functional slice principles. Each phase must deliver working, testable functionality. For full-stack features, ensure each slice includes both frontend and backend components needed for that functionality to work end-to-end.

### Task Boundary Definition

### Complete PRD Requirement Transfer

<critical>
Every functional requirement from the PRD must be explicitly included in task blocks. Implementation agents do not have access to the PRD, so missing requirements = missing implementation.
</critical>

**Universal Principles**:

- All user-facing functionality must be explicitly documented
- All business rules and conditional logic must be clearly specified
- All data handling requirements must be captured
- All error scenarios and user feedback must be defined
- All validation rules must be included

**Implementation Guidance**:

- Focus on complete coverage rather than specific content types
- Ensure all PRD requirements have corresponding task implementation
- Document behavior, not just technical specifications

Within each block, create tasks that:

**Provide Clear Research Direction**:

```markdown
Research [specific-service] pattern (src/app/shared/services/[service-name].service.ts)
Focus on: [specific aspects relevant to the feature requirements]
```

**Define Implementation Scope**:

```markdown
**Implementation Scope**: This creates complete functionality following established patterns. All requirements should be properly implemented and testable.
```

**Reference Specific Constraints**:

```markdown
**Must Follow**:

- [applicable coding rules]
- [exact patterns from referenced services]
- [architectural constraints identified in research]
```

**CRITICAL: NO CODE IN TASKS**:

<critical>
YOU ARE ABSOLUTELY FORBIDDEN FROM INCLUDING ANY OF THE FOLLOWING IN TASK DOCUMENTATION:

- Code snippets or examples (TypeScript, HTML, CSS, SQL, YAML examples, etc.)
- Exact method signatures or function definitions
- Step-by-step implementation instructions
- Detailed pseudo-code or algorithmic steps
- Specific variable names or class structures
- Line-by-line implementation guidance

VIOLATION OF THIS RULE RESULTS IN UNUSABLE TASK DOCUMENTATION.

Implementation agents are skilled developers who:
- Can read existing code patterns and replicate them
- Understand how to implement features based on high-level requirements
- Follow coding rules and architectural patterns independently

YOUR ROLE: Point them to the right patterns and define the outcomes. THEIR ROLE: Figure out how to code it.
</critical>

**What You SHOULD Provide**:

- Research directions: "Study the authentication service pattern in src/app/core/auth/auth.service.ts"
- Outcome definitions: "Implement complete user login flow with JWT token handling"
- Constraint references: "Must follow rules-for-agents/coding/002-frontend-angular-primeng-rules.md"
- Validation criteria: "User can log in, receive token, and access protected routes"

**What You MUST NOT Provide**:

- How to write the code
- What the code should look like
- Specific implementation steps beyond "research X, implement Y, validate Z"

### Validation Point Design

For each checkpoint, define:

**Deliverable Statement**:

- Specific, testable outcome that can be verified
- Clear success criteria
- Example: "Service layer can handle all [feature] operations with proper error handling"

### Implementation Phase Scope Validation

<critical>
Self-check criteria must match the working functionality scope of each phase exactly. Each phase should deliver testable end-to-end functionality within its defined slice.
</critical>

**Before creating self-check criteria, ensure**:

- **Working functionality focus**: Validate what the user can actually do and test
- **End-to-end scope**: Even minimal phases should work from user action to visible result
- **Incremental complexity**: Later phases build upon and enhance earlier working functionality

**Common Scope Violations to Avoid**:

- Creating phases that only set up infrastructure without working user functionality
- Asking about features not yet implemented in the current phase scope
- Validating architectural completeness instead of working user flows

**Validation Questions Template**:

```markdown
**Self-Check Before Checkpoint**:

- [Question 1: Can user perform the main action this phase enables?]
- [Question 2: Does the working functionality match the deliverable exactly?]
- [Question 3: Are the testable outcomes clearly demonstrable?]
- [Question 4: Is the foundation solid for building the next functional slice?]
```

**Failure Impact**:

- Explain why this checkpoint matters for user-testable functionality
- What working features break if this isn't solid
- Example: "If This Fails: Users can't complete the basic flow, making advanced features meaningless"

### Planning Summary & Completion

Create a final summary including:

**Selected Strategy**: [Simple/Complex/Architectural] with [X] phases
**Rationale**: Why this functional slice approach was chosen

**Implementation Phases Overview**:

- Phase 1: [Name] - [Working functionality delivered]
- Phase 2: [Name] - [Additional working functionality delivered]
- etc.

**Risk Mitigation**:

- How user-testable checkpoints prevent major rework
- What functionality failures are being caught early through validation

**Efficiency Considerations**:

- How functional slices deliver value incrementally
- How agents can work efficiently within testable boundaries

## Quality Assurance Requirements

Before completing this phase:

- Ensure functional slice strategy aligns with feature complexity and user validation needs
- Confirm implementation guidance provides clear boundaries without micromanagement
- Verify that phases deliver testable working functionality users can interact with

## Task Planning Completion Signal

When planning is complete, present your planning summary and then AUTOMATICALLY proceed to the next phase (Task Documentation & Context Setup).

Signal completion with:

**[TASK PLANNING COMPLETE - PROCEEDING TO DOCUMENTATION]**

**Phase 2 Output**: Implementation phases with working functionality slices and user validation criteria
