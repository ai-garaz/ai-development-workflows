# Self-Improvement

## The Problem

AI agents make mistakes — they misuse tools, skip procedure steps, miss knowledge they should have found, or report success when something actually failed. In a multi-session system where each session starts fresh, the same mistakes can recur indefinitely unless there's a systematic way to track and address them.

## Our Approach

We track agent failure patterns as regular tasks, the same way we track any other work item. Each failure category gets its own long-running task with a weekly activity log that records occurrences, root causes, and improvements made.

## Failure Categories

We currently track four categories, identified through months of weekly reviews:

### 1. Agent Confusion
False success reports (agent says it did something but didn't), tool misuse (wrong parameters, wrong tool for the job), and domain knowledge errors (confidently wrong about a business rule).

### 2. Procedure Not Followed
Skipped steps in documented procedures, auto-processing when manual review was required, missing quality gates.

### 3. Search Miss
Knowledge that existed in the system but wasn't found — either because the agent didn't search, searched with wrong terms, or the knowledge wasn't indexed.

### 4. Tool Failure
Systematic tool problems like shell working directory resets, relative path errors, or documentation gates causing friction.

## How It Works

### Weekly Reviews

Every Friday, the agent runs an end-of-week review that includes:

1. **Session analysis**: Review all sessions from the week for failure patterns
2. **Categorization**: Map each failure to one of the tracked categories
3. **Root cause**: Why did this happen? Is it a procedure gap, a knowledge gap, or a tool limitation?
4. **Activity log update**: Add the week's findings to the relevant task

### Improvement Cycle

When a pattern is identified:
1. A specific fix is proposed (procedure edit, knowledge entry, tool adjustment)
2. The fix is implemented
3. Subsequent weeks track whether the fix reduced occurrences

### Example Activity Log Entry

```yaml
- date: "2026-03-14"
  note: |
    Week 11 review. 3 occurrences of procedure_not_followed:
    - Session abc123: Skipped email triage step 3 (auto-processed a non-trivial email).
      Root cause: The "non-trivial" criteria were ambiguous.
      Fix: Rewrote triage criteria with explicit examples.
    - Session def456: Missed quality gate after final implementation phase.
      Root cause: The procedure said "suggest running review-execution" — agent treated it as optional.
      Fix: Changed wording to "invoke /review-execution" — made it a directive, not a suggestion.
    - Session ghi789: Processed support email without checking knowledge base first.
      Root cause: Support procedure didn't have a mandatory search step.
      Fix: Added "search knowledge/support-cases/ before responding" as step 1.

    Running totals: 47 occurrences over 6 weeks, down from 12/week to 3/week.
```

## What We Learned

- **Track patterns, not incidents.** Individual mistakes are noise. Patterns are signal. The weekly aggregation is what makes this useful — it turns "agent messed up" into "agent consistently struggles with X, and here's why."

- **Procedure wording matters enormously.** "Consider doing X" vs "Do X" vs "You must do X" produce very different compliance rates. When we found a procedure wasn't being followed, the fix was often just changing the language from suggestive to directive.

- **Tool failures are the hardest to fix.** Agent confusion and procedure violations can be addressed by editing text files. Tool failures often require code changes, workarounds, or accepting the limitation. Tracking them separately prevents the frustration of mixing fixable and unfixable problems.

- **Improvement compounds.** Week 1: 12 failures. Week 6: 3 failures. The system gets measurably better over time because each failure is analyzed, understood, and addressed. The activity logs provide proof of improvement that's visible to both agents and operators.

- **Agents can be their own quality reviewers.** The same agent that made the mistake analyzes it in the weekly review and proposes the fix. This works because the review happens in a dedicated session with explicit instructions to look for problems — a different mindset than the original task-focused session.
