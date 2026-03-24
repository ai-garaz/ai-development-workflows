# Multi-Agent Architecture

## Overview

Our system runs multiple AI agents, each specialized for a domain — development, work/email management, personal tasks, and system administration. A lightweight Node.js orchestrator routes Slack messages to the right agent and manages Claude Code sessions.

## Components

### Orchestrator

A Node.js daemon that handles:
- **Message routing**: Slack messages arrive → router determines which workspace handles them (based on channel, mentions, keywords)
- **Session management**: Creates new Claude Code sessions for new threads, resumes existing ones for thread replies
- **Lifecycle**: Heartbeats, scheduled jobs, graceful shutdown

The orchestrator has **zero AI logic**. It doesn't call any LLM APIs. It just moves messages between Slack and Claude Code processes. All intelligence lives in the workspace CLAUDE.md files.

### Workspaces

Each agent has its own workspace — a separate git repository with:
- `CLAUDE.md` — the agent's complete rules, principles, and operational knowledge
- `SOUL.md` (optional) — the agent's identity and values
- `procedures/` — step-by-step workflows for specific tasks
- `knowledge/` — domain-specific reference material
- `tasks/` — tracked work items
- `daily-logs/` — session history and notes

Workspaces are git submodules of the parent repository. This means:
- Each agent's rules are independently versioned
- Changes to one agent don't affect others
- The parent repo tracks which version of each workspace is deployed

### Bots

Slack apps that serve as transport. Each workspace has its own bot (so messages show different names/avatars). Bots are "dumb pipes" — they receive messages and forward them to the orchestrator. They have no logic.

## Design Principles

1. **Claude Code IS the agent** — the orchestrator is just plumbing. All behavior is defined in text files (CLAUDE.md, procedures, knowledge).

2. **Config-driven** — adding a new workspace means editing a YAML config file and writing a CLAUDE.md. No code changes to the orchestrator.

3. **Bots are dumb pipes** — transport only. A bot could be replaced with email, SMS, or a web interface without changing any agent logic.

4. **Everything in git** — agent rules, procedures, knowledge, tasks, daily logs. All versioned, all reviewable.

5. **Graceful degradation** — if the memory system is down, agents can still read files directly. If a tool is unavailable, agents report what they need and the operator can intervene.

## Message Flow

```
User posts in Slack
    → Bot receives webhook
    → Orchestrator router determines workspace
    → Dispatcher creates/resumes Claude Code session
    → Agent processes message (reads CLAUDE.md, uses tools, generates response)
    → Response streamed back to Slack thread
```

## What We Learned

- **Keep the orchestrator thin.** Every time we considered adding "smart" routing or AI-powered message classification to the orchestrator, we found it was better to keep it in the workspace rules. Simple pattern matching (channel + mention) handles 95% of routing needs.

- **Separate repos per workspace is worth the complexity.** It seemed like overhead at first, but it means agents can commit to their own repos without coordination. Different agents can be at different stages of development.

- **SOUL.md matters more than expected.** A short identity document (values, communication style, what the agent won't do) produces noticeably more consistent behavior across sessions than CLAUDE.md rules alone. It's the difference between an agent that follows instructions and one that has a personality.
