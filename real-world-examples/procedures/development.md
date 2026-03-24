# Development — Implementation Procedure

Execution instructions for implementing features. This procedure takes the outputs of the [explore-task](explore-task.md) procedure (task analysis, PRD, implementation plan) and executes them. It augments the agent's native capabilities with project-specific knowledge it cannot derive on its own.

## Scope of This Procedure

These steps define the baseline. If the task requires additional debugging rounds, more thorough reference file study, or investigation beyond what's listed — do that. The steps below are a floor, not a ceiling.

## Trigger

- After completing explore-task with approved analysis, PRD, and implementation plan
- "start coding", "implement [task]", "let's build this"

---

## 1. Input Validation

Before any implementation work, verify that the planning artifacts exist:

| File | Created by |
|---|---|
| Task analysis | explore-task — step 5 |
| PRD | explore-task — step 6 |
| Task breakdown | explore-task — step 7 |
| Codebase context | explore-task — step 7 |

If any are missing, warn the operator and suggest running the explore-task procedure first. Do not proceed to implementation without these inputs.

---

## 2. Review Plan

Validate the planning artifacts before implementation begins.

**Cross-session context**: If this is a new session (not the same one that produced the plan), first read all input files so the review has full context.

**Invoke the review-plan command** to audit the PRD and implementation plan against the original request.

**Additionally**, verify project-specific concerns that the general audit may not cover:
- For each component the plan reuses: does it actually expose the inputs, outputs, and slots the plan assumes?
- For each type the plan extends or creates: does the existing type have the fields the plan expects?
- For each service the plan injects: is it available in the component's module/providers?
- For each form control in edit mode: is it active or disabled? Will it emit values that need to be stripped?
- Scope gaps: are there requirements in the original issue that the PRD doesn't cover?
- Over-engineering: are there features or phases that weren't requested?
- Testability: can each phase deliverable actually be validated by the user?
- **Coding rules**: does the codebase context have an "Applicable Coding Rules" section? If not, identify the applicable rules now.

**Output**: A numbered list of checks with outcomes. For each flagged item, describe what's wrong and what the fix should be. Update the PRD and task breakdown to close all gaps before proceeding.

---

## 3. Project Bootstrap

### Set Up

1. Determine which project/repo you are working on
2. Set your project root to the **worktree path** (not the main checkout)
3. Read the project's architecture docs and coding rules
4. **Recall coding learnings**: search memory for past coding-related learnings before starting any work

### Session Continuity

This workflow often spans multiple sessions. Every session must be able to pick up where the last one left off.

**At session start**, locate the worktree and read all task state files (task analysis, PRD, tasks, codebase context, and active context).

**At session end** (or at any phase boundary), update the active context with:
- Current status and phase
- What was completed this session
- Key decisions made and why
- What remains and what to do next
- Any blockers or open questions

**This is the single most important cross-session artifact.** If it's current, the next session can resume cleanly. If it's stale, the next session starts blind.

### Create Active Context

Create the active context file with:
- Current status (starting implementation)
- Implementation plan phases overview
- First phase scope and objectives
- Decisions or constraints from the review-plan step

**Confirmation gate**: Present the active context to the operator. Begin implementation only after operator confirms.

---

## 4. Implementation Discipline

Build-time guardrails that prevent common failure modes.

### Read Coding Rules First

Before implementing anything:
1. Check the codebase context for an "Applicable Coding Rules" section. If it exists, read every rule file listed there.
2. If no rules section exists, fall back to the rules README and identify applicable rules based on what the task touches.

### Study Reference Files Completely

When given reference files to study, read them in full. Understand exact patterns, structure, implementation approach, and architectural decisions. Note component composition strategies, async operations, state management, and data flow patterns as implemented. Do not skim. Consistency with existing patterns is how a codebase stays maintainable.

### Implement Exactly What Was Requested

- No additional features beyond what was asked for.
- No tests unless explicitly requested.
- No changes to code outside the defined scope.

### Debugging Protocol

When you hit a problem during implementation:

1. **Hypothesize broadly** (5-7 potential sources across different layers: API, service, component, template, data flow, configuration), then **distill to 1-2** most likely candidates.
2. **Validate with targeted diagnostics**: insert specific console.log statements to test your top hypotheses. For each diagnostic, note what result you expect if your hypothesis is correct.
3. **3-attempt ceiling**: if 3 fix attempts fail, change strategy. Search the web for the specific error messages. Document what was tried and what failed. Present findings and ask for guidance.
4. **Clean up**: remove ALL debugging code after the fix is confirmed.

### Problem Escalation

If you encounter a difficult problem after 1-2 implementation attempts at the same approach, switch to structured debugging. Do not persist with approaches that are not working.

### Self-Check at Phase Boundaries

After completing each implementation phase, verify before moving on:
- Is the deliverable testable end-to-end?
- Does the phase's functionality work as specified?
- Update the active context with what was completed and what's next.

Do not start the next phase on a broken foundation.

---

## 5. Quality Gates

Formal review checkpoints that catch issues at natural transition points. These are not optional — they consistently find problems.

### After Final Implementation Phase → Review Execution

After completing the last phase, invoke the review-execution command to audit the implementation. The review checks:
- PRD completeness — walk each requirement and verify it's implemented
- Coding rules compliance — verify changed files follow applicable rules
- Drift — did implementation deviate from the plan? If so, is the deviation justified?
- Obvious bugs in the diff

### Pre-PR Validation

Before creating the PR, a lightweight self-review:
- Walk each PRD requirement one more time
- Check for leftover debugging code
- Verify the active context is up to date
- Confirm clean commits with proper messages

---

## 6. Delivery

### Git Workflow

- Feature branch and worktree should already exist from the exploration phase
- All work happens in the worktree — commits, file edits, builds
- Commit regularly
- **Always commit feature documentation alongside code** — PRD, tasks, codebase context, and active context are part of the deliverable
- Push to the feature branch when commits are ready
- **Never push to main** unless directly authorized

### Pull Request

After pre-PR validation passes, create a PR with:
- Summary (what this implements, 2-3 sentences)
- Task code and link
- Changes (bulleted list by area)
- PRD reference
- Test plan (specific steps to verify)

### Post-Merge Cleanup

After the PR is merged:
1. **Discover state** — check for manual changes made by others on the PR before merging
2. **Present overview** — list documentation to update, cleanup to do
3. **Execute after approval** — update docs, close issue, remove worktree, delete branches

---

## 7. Anti-Patterns

Failure modes learned from experience during implementation.

**Do not persist with failing approaches.** After 1-2 failed attempts at the same approach, change strategy. Read the error differently, search the web, or ask for guidance. Repeated attempts at the same broken approach waste time and often make things worse.

**Do not "improve" adjacent code.** Unauthorized modifications in an established codebase introduce subtle bugs. Consistency with existing patterns is more valuable than local code quality improvements. Stay within the scope of the assigned task.

**Do not skip active-context updates.** Every phase boundary and every session end must update the active context. A missing update means the next session starts blind and may redo or contradict work already done.
