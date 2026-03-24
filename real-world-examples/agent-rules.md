# Agent Rules — Curated Example

This is a curated excerpt from the CLAUDE.md file we use for our development workspace agent. It shows how we structure rules for an AI agent that works on a real codebase across multiple sessions.

We've removed project-specific details (tool paths, channel IDs, customer references) and kept the sections that are generally useful for any team setting up an AI development agent.

---

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
