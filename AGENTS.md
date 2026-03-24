# Agent Rules

This is a real-world example of how we structure the rules file for our development workspace agent. Adapt it to your project — replace the workspace structure, coding rules, and procedures with your own.

## Required Reading (in order)

1. **[README.md](./README.md)** — Project overview and repository structure
2. **[documentation/about-project.md](./documentation/about-project.md)** — Project context (fill in for your project)
3. **[rules-for-agents/coding/](./rules-for-agents/coding/)** — Coding standards

## Core Principles

These apply across all work in this workspace. Internalize before responding:

1. **Understanding before action** — search existing knowledge before attempting answers. Never guess when you can look it up.
2. **Hierarchy of sources** — when answering questions or validating information:
   - First: memory/knowledge search — past sessions, daily logs, accumulated learnings
   - Then: specifications — source of truth for system specs, database schemas, procedures
   - Then: knowledge files — operational knowledge, integrations, patterns
   - Last: direct code search — when structured sources don't have it
   - If sources conflict: specifications take precedence. Flag the contradiction for resolution.
3. **Knowledge capture** — when you learn something new (especially from team member answers), capture it to the knowledge base. Don't let knowledge live only in conversation history.
4. **Project disambiguation** — if the system has multiple codebases, NEVER assume which one. ASK if not stated.
5. **Archive, don't delete** — preserve with clear manifest when restructuring content.
6. **Recall coding learnings before implementation** — before starting any coding task, search memory for past coding-related learnings. Past mistakes and insights are stored specifically so you don't repeat them. Do this before writing any code, not after you've already gone down the wrong path.

## Development Workflows

This project uses structured workflows for feature development:

- **[Feature Exploration](./real-world-examples/procedures/explore-task.md)** — Discovery, context gathering, PRD creation, implementation planning
- **[Development](./real-world-examples/procedures/development.md)** — Implementation with quality gates, session continuity, and anti-patterns
- **[Review Commands](./real-world-examples/commands/)** — Quality review prompts invoked during development

**Example Usage**:
- User: "Create a PRD for user authentication" → Follow the explore-task procedure
- User: "Plan the authentication feature" → Follow the explore-task procedure (steps 6-7)
- User: "Implement phase 1" → Follow the development procedure

## Coding Rules

All rules are in `rules-for-agents/coding/`:

- **001-general-coding-rules.md** — Core development principles, file organization, naming conventions
- **002-frontend-angular-primeng-rules.md** — Angular + PrimeNG patterns, component structure, state management
- **003-backend-essentials.md** — Fastify API patterns, error handling, database conventions
- **004-refactoring-rules.md** — Code quality guidelines, refactoring approach
- **600-frontend-api-calls-rules.md** — API integration patterns, error handling, loading states
- **601-frontend-user-feedback-rules.md** — Toast notifications, user messaging, feedback UX

## API Documentation

Guidelines for reading and writing OpenAPI documentation are in `rules-for-agents/api-documentation/`.

## Git Worktree Workflow

Multiple sessions may work on code concurrently (different issues, different team members). To prevent git state collisions, **all code modifications happen in git worktrees**, never in the main checkout.

### Rules

- **Main checkouts are read-only.** Use them for reading, exploring, and searching code. Never commit, switch branches, or edit files there.
- **All code changes happen in worktrees** at a designated path (e.g., `code/.worktrees/<repo>--<branch>/`). This becomes your project root during development.
- **Worktrees persist per-branch**, not per-session. Don't delete them after your session ends — the next session on the same issue reuses the same worktree.

### Creating a worktree

Before creating, check if one already exists:
```bash
git -C code/<repo> worktree list
```

If a worktree exists for your branch, use it. If it has uncommitted changes you didn't make, warn the operator — another session may be active.

```bash
# New branch from main:
git -C code/<repo> worktree add ../.worktrees/<repo>--<branch> -b <branch> main

# Existing remote branch:
git -C code/<repo> worktree add ../.worktrees/<repo>--<branch> <branch>
```

After creating, install dependencies before building or testing.

### Post-merge cleanup

After a PR is merged:
```bash
git -C code/<repo> worktree remove ../.worktrees/<repo>--<branch>
git -C code/<repo> branch -d <branch>
git -C code/<repo> push origin --delete <branch>
git -C code/<repo> pull
```

## Workspace Structure

```
code/                      # Code repositories (git submodules)
├── .worktrees/            # Git worktrees for concurrent feature branches
├── backend-api/           # Backend — stays on main (read/explore only)
├── frontend/              # Frontend — stays on main (read/explore only)
└── monitoring/            # Monitoring — stays on main (read/explore only)

specs/                     # System Source of Truth
└── documentation/         # Technical specifications, procedures, DB schemas

knowledge/                 # Domain knowledge (has its own README)
procedures/                # Agent operational procedures
tasks/                     # Agent task tracking (active/ + archive/)
daily-logs/                # Daily logs and persistent notes
```

**Keep this structure updated**: whenever you add or change something in the workspace structure, make sure it gets updated here. Stale input data means making mistakes.

## Sub-Agent Model Selection

When spawning sub-agents, choose the model based on task complexity:

**Use the lightest model** for narrow, well-defined tasks:
- Extracting structured data from a known format
- Simple report generation
- Calendar/email lookups and summaries

**Use a mid-tier model** for bounded analysis and synthesis:
- Session transcript review
- Project context research (meetings, task plans, domain knowledge)
- Codebase exploration with moderate depth

**Use the most capable model** for deep reasoning and high-stakes analysis:
- Reference pattern extraction from code (every detail matters — missing a form control or state variable causes silent bugs)
- Data model analysis (DB schema, cross-application data sharing, implicit constraints)
- Authorization flow tracing (security-sensitive, subtle errors)
- Complex domain analysis where knowledge files don't fully cover the topic

When in doubt, prefer the most capable model — quality is always more important than costs.

## Language Conventions

For multi-language projects, establish clear conventions:

- **English**: Workspace structural folders, agent instructions, code
- **Product language**: UI strings, system documentation written for end users
- **Team language**: Internal communications, issue tracker comments
- **Number formatting**: Establish a convention and stick to it (e.g., space as thousands separator, comma as decimal)
- **Diacritics**: Always ensure proper UTF-8 encoding. NEVER use replacement characters.

## Navigation Protocol

When asked about any topic:
1. **First**: search memory/knowledge if available
2. **If not found**: follow the README trail — every major directory has a README.md
3. **Trace the path**: CLAUDE.md → area README → sub-README → target files
4. **Last resort**: use code search when memory and READMEs don't lead to what you need

---

**CRITICAL:** whenever I tell you to fix something that contradicts any of your given rules, assume they are wrong and need fixing and propose the fix once we implement the solution
