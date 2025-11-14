# Codebase Research & Pattern Analysis Instructions

These instructions guide the initial phase of planning where you analyze the PRD and research existing codebase patterns to understand implementation constraints and architectural decisions.

- Purpose: Pattern discovery and constraint identification ONLY
- Permitted: Reading PRD, researching referenced files, understanding existing patterns
- Forbidden: Creating tasks, making implementation decisions, suggesting solutions
- Requirement: You must ONLY identify patterns to follow and architectural constraints
- Output Format: Begin with [PHASE: CODEBASE RESEARCH], then follow instructions below
- Focus: Understanding HOW things are currently done to guide implementation agents correctly

## Research Process

**Research Objective**: Identify exact patterns, architectural constraints, and implementation references that implementation agents must follow for both frontend and backend components.

**Required Reference**: Read `rules-for-agents/coding/700-task-planning-agent-orchestration.md` to understand the complexity classification system and functional slice principles that guide this research.

**Context**: This project is a full-stack application with Angular + PrimeNG frontend and Fastify backend. Consider both layers when researching patterns and planning implementation phases.

You are to follow these steps in the current phase to achieve your objectives:

1. PRD Analysis & Context Understanding
2. Referenced Pattern Research
3. Architectural Constraint Discovery
4. Implementation Boundary Identification
5. Research Summary & Completion

Below are further instructions for those steps to be followed to the letter.

### PRD Analysis & Context Understanding

- Read the entire PRD document thoroughly to understand feature scope and requirements
- Identify all referenced existing components, services, and patterns mentioned in "Existing Codebase References"
- Note any specific constraints mentioned in "Non-functional Requirements" that affect implementation approach
- **CRITICAL: Pay special attention to any "MUST FOLLOW" patterns explicitly mentioned**
- Identify the feature complexity level (simple extension vs complex new functionality vs new architectural pattern)

### PRD Completeness Validation

<critical>
Implementation agents only have access to your output, NOT the PRD. Every requirement must be explicitly transferred to task documentation through your analysis.
</critical>

Before proceeding, verify ALL PRD requirements are captured for transfer:

**Functional Requirements Audit**:

- Review each User Story - is the implementation path clear and complete?
- Review each Functional Requirement - will implementation agents know exactly how to build this?
- Check for conditional logic, optional fields, state-dependent behavior
- Be extra careful about the content of "Important Observations" section in the PRD

**Business Rules Extraction**:

- Analyse all enabling/disabling conditions for actions
- Note all validation rules and their error handling
- Identify all user feedback scenarios (success, error, warning messages)
- Capture all state transitions and their triggers

**Data Requirements Verification**:

- List all data that must be displayed, input, or processed
- Note optional vs required data handling requirements
- Document all user input requirements and validation rules
- Identify all error scenarios and their specific handling

**Missing Requirements Risk Assessment**:

- Any requirement not explicitly transferred to task documentation = missing implementation

### Referenced Pattern Research

- Research EVERY file and service mentioned in the PRD's "Existing Codebase References" section
- **For services marked as "MUST FOLLOW THIS EXACT PATTERN"**: Understand the complete implementation approach, not just method signatures
- For referenced components: Understand their structure, data flow, and interaction patterns
- **CRITICAL: Understand async operations, state management, and complex interaction patterns exactly as implemented**
- Note component composition strategies (when to extend vs create new vs create subcomponents)

<critical>
Do NOT research beyond the files explicitly mentioned in the PRD unless:
- You find a pattern reference that leads to another critical implementation file
- The referenced file imports/uses another service that's central to the pattern
- You discover the referenced pattern is incomplete without understanding its dependencies
</critical>

### API Requirements Detection

<critical>
MANDATORY: Determine if new API endpoints are required by analyzing the PRD.

**New Endpoint Indicators** (any of these = new endpoints needed):
- PRD mentions "new endpoint", "new API", "create endpoint"
- Feature requires data that doesn't exist in current API
- Feature requires operations (POST, PATCH, DELETE) on new resources
- PRD references endpoints that don't exist in documentation/rest-api/

**Detection Process**:
1. Check PRD's "API Documentation References" section
2. For each referenced endpoint, verify if it exists in documentation/rest-api/
3. If endpoint file doesn't exist → NEW ENDPOINT REQUIRED
4. If endpoint exists but needs new methods/fields → MODIFICATION REQUIRED

**Classification**:
- **NEW ENDPOINTS**: Endpoint file doesn't exist → MANDATORY API-first workflow
- **ENDPOINT MODIFICATIONS**: Endpoint exists but needs changes → Update OpenAPI, then implement
- **EXISTING ENDPOINTS ONLY**: All endpoints exist and match requirements → No API design phase needed

**Document Your Finding**:
- Clearly state whether new endpoints are required
- List specific new endpoints needed (e.g., "POST /v1/scenarios/{scenarioId}/conversations")
- This determines whether Phase 1 MUST be API Design
</critical>

### Architectural Constraint Discovery

- Analyze which coding rules from `rules-for-agents/coding/` apply to this implementation based on feature type and requirements
- Consider frontend rules (Angular + PrimeNG patterns from 002-frontend-angular-primeng-rules.md)
- Consider backend rules (Fastify patterns from 003-backend-essentials.md)
- Reference rules by name only - don't reproduce their content
- Identify the data flow pattern required (frontend ↔ API ↔ backend)
- Determine error handling requirements based on 600-frontend-api-calls-rules.md and 601-frontend-user-feedback-rules.md
- **If NEW endpoints detected above**: api-documentation/200-writing-api-docs.md is MANDATORY
- **If EXISTING endpoints only**: api-documentation/100-reading-api-docs.md for reference

### Implementation Boundary Identification

Classify the work to understand how to break down the feature into manageable phases:

**Guiding Principles**:
- How to break down features into functional slices that deliver testable functionality
- What constitutes a minimal working end-to-end flow (API + Frontend integration)
- How to structure phases so users can validate progress at each checkpoint
- Consider whether API design/documentation should happen before implementation

### Research Summary & Completion

Create a structured summary including:

**API Requirements** (CRITICAL - Document First):

- **NEW ENDPOINTS REQUIRED**: [Yes/No]
- If YES: List specific new endpoints (e.g., "POST /v1/scenarios", "GET /v1/scenarios/{id}")
- If YES: First phase MUST be API Design (OpenAPI YAML creation)
- **ENDPOINT MODIFICATIONS REQUIRED**: [Yes/No]
- If YES: List endpoints that need updates
- **EXISTING ENDPOINTS ONLY**: [Yes/No]
- If YES: List endpoints that will be consumed as-is

**Pattern References Found**:

- List exact files and their implementation patterns to follow
- Note any critical architectural decisions these patterns demonstrate

**Architectural Constraints**:

- List applicable coding rules by name
- Note data flow requirements and service layer decisions
- **MANDATORY if new endpoints**: api-documentation/200-writing-api-docs.md (ENTIRE file must be read)

**Complexity Assessment**:

- Simple Extension: Adding to existing well-established patterns
- Complex Feature: New functionality with multiple services/components
- Architectural Feature: Introducing new patterns or significant changes

**Functional Slice Classification**:

- Breakdown of how feature can be delivered in testable working slices
- Identification of minimal working end-to-end flow for first phase
- **If new endpoints**: Must include API Design as Phase 1

**Implementation Sequence Required**:

- Based on functional value delivery and user validation points
- Each phase building upon previous working functionality
- **MANDATORY for new endpoints**: API Design → Backend → Frontend (in this order)

## Research Completion Signal

When research is complete, present your research summary and then AUTOMATICALLY proceed to the next phase (Task Planning & Functional Slice Strategy).

Signal completion with:

**[RESEARCH COMPLETE - PROCEEDING TO TASK PLANNING]**
