# Task Management

## Overview

Every workspace tracks work items as validated YAML files. Tasks follow a schema with required fields, lifecycle states, and activity logs. A validation tool enforces the schema automatically after every edit.

## Why YAML, Not a Database

- **Agents can read and write them with standard file tools** — no database driver needed
- **Version-controlled** — full history of every change in git
- **Human-readable** — operators can review tasks in any text editor
- **Searchable** — the memory system indexes task files alongside other knowledge

## Task Schema (Simplified)

```yaml
id: "WRK-015"
title: "Process March bank statements"
status: active                  # active | completed | cancelled
created: "2026-03-15"
due: "2026-03-20"
source: "Monthly calendar"
context: |
  Bank statements for February arrived. Process through the standard
  financial pipeline — XML to YAML, categorize transactions, reconcile.

success_criteria:
  - "All February transactions categorized"
  - "Cash flow entries created for each transaction"
  - "Reconciliation report generated"

activity_log:
  - date: "2026-03-15"
    note: "Task created from monthly calendar trigger"
  - date: "2026-03-16"
    note: "Downloaded XML from bank portal, converted to YAML. 47 transactions."
  - date: "2026-03-17"
    note: "Categorization complete. Found 2 transactions needing manual review — flagged for operator."
```

## Lifecycle

```
created → active → completed → archived
                 → cancelled → archived
```

- **Active**: has success criteria, may have a due date, gets activity log entries as work progresses
- **Completed**: all success criteria met, outcome documented, archived to `tasks/archive/YYYY/WXX/`
- **Cancelled**: reason documented, archived

## Validation

A PostToolUse hook runs the validation tool automatically after every Write/Edit to task files. If validation fails, errors appear in the agent's context — it must fix them before continuing.

Available commands:
- `validate-tasks next-id` — get the next sequential ID before creating a task
- `validate-tasks scan` — validate all tasks in the workspace

## Activity Logs

The key insight: **activity logs capture decisions, not just timestamps.** An entry that says "Processed 47 transactions" is less useful than one that says "Processed 47 transactions. Found 2 from vendor X that don't match any category — categorized as 'Other/Review' and flagged for operator. Considered creating a new category but decided against it since this vendor appears to be a one-time payment."

## Archival and RAG-Readiness

Before archiving a completed task, the agent asks: "If a similar task arrives in 6 months, what would an agent need to know?" The activity log should capture approach, key decisions, and pitfalls. If the task produced learnings worth finding via search, they're also stored in the memory system.

## What We Learned

- **Workspace-specific ID prefixes prevent collisions.** Each workspace uses a different prefix (WRK, DA, etc.), so task IDs are globally unique across the system without coordination.

- **Validation-on-write catches errors immediately.** Early on, agents would create malformed tasks that only surfaced later. The PostToolUse hook means the agent sees the error within seconds and fixes it in the same session.

- **Proactive closure is better than waiting.** Agents are instructed to suggest closing tasks when success criteria are met, rather than waiting for the operator to notice. This keeps the active task list clean.

- **Recurring tasks create their next instance on completion.** Monthly tasks (bank statements, reviews) automatically spawn the next month's instance when the current one is archived. No manual scheduling needed.
