# System Architecture Insights

This section describes patterns from our production multi-agent AI system. We run four specialized agents that handle development, project management, personal productivity, and system administration — all coordinated through a lightweight orchestrator.

**Important**: These documents are shared as inspiration, not as a blueprint. They describe what works well for us after months of iteration. They're not exhaustive, and they skip implementation details intentionally. If you're considering building something similar, use these as input to your own thinking — not as a specification to follow.

## Documents

| Document | What it covers |
|----------|---------------|
| [Multi-Agent Architecture](multi-agent-architecture.md) | How agents are organized, how the orchestrator routes messages, design principles |
| [Memory System](memory-system.md) | Multi-source indexed memory for long-term knowledge retention across sessions |
| [Task Management](task-management.md) | YAML-based task tracking with validation, lifecycle, and archival |
| [Agent Communication](agent-communication.md) | How agents talk to each other, spawn new sessions, and schedule deferred work |
| [Self-Improvement](self-improvement.md) | How agents track their own failure patterns and systematically improve |

## Key Design Principle

The single most important decision in our system: **the AI model IS the agent**. The orchestrator is pure plumbing — it routes messages, manages sessions, and handles lifecycle. All intelligence, all decision-making, all domain knowledge lives in the CLAUDE.md rules files and the agent's context. This means:

- Adding a new agent = adding a config entry + writing a CLAUDE.md
- Agent behavior is versioned in git, reviewable in PRs, and debuggable by reading text files
- The orchestrator has zero LLM calls — it's a Node.js daemon that moves messages between Slack and Claude Code sessions
