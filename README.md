# AI Development Workflows

A collection of structured prompts, coding rules, and real-world examples for AI-assisted software development. Designed to help development teams work more effectively with AI coding assistants like Claude Code, GitHub Copilot, Codex, and Gemini.

## What's Inside

### 1. Developer Toolkit

Drop-in rules and workflow prompts you can add to any project:

- **Agent Workflows** — Structured prompts for PRD creation, task planning, and implementation
- **Coding Rules** — Language and framework-specific best practices (Angular 20+, PrimeNG 20+, Fastify/TypeScript)
- **API Documentation Standards** — Guidelines for reading and writing OpenAPI documentation

The toolkit lives at the root of this repository. `CLAUDE.md` and `AGENTS.md` are auto-detected by AI assistants when placed in your project root.

**Quick start:**
1. Copy `CLAUDE.md`, `AGENTS.md`, `documentation/`, and `rules-for-agents/` to your project root
2. Create `documentation/about-project.md` with your project details (see template)
3. AI assistants auto-detect the rules and follow the workflows

The coding rules are optimized for **Angular 20+ / PrimeNG 20+ / Fastify (TypeScript)**, but the workflow patterns (PRD → Planning → Building) are framework-agnostic and can be adapted to any stack.

### 2. Real-World Examples

Prompts and workflows from our production AI-assisted development system. These are the actual rules, commands, and procedures that power our day-to-day development with AI agents:

- **Agent rules** — how we structure our CLAUDE.md for a development agent
- **Commands** — review and quality prompts we invoke during development
- **Procedures** — step-by-step workflows for feature exploration and implementation
- **Task lifecycle example** — a complete feature from discovery through delivery

These are shared as practical inspiration — patterns that work well for us. Every team's codebase, tools, and process are different, so adapt what resonates and ignore what doesn't.

See [`real-world-examples/`](real-world-examples/) for details.

### 3. System Architecture Insights

For teams considering building a multi-agent AI development system: an overview of patterns that work well in our production setup.

**Important**: This is not an exhaustive guide or a turnkey system. It's a curated look at design decisions we've found valuable after running a multi-agent system in production. It's meant to inform your own thinking if you decide to build something similar — not as a blueprint to follow.

Topics covered:
- Multi-agent architecture and orchestration
- Memory and knowledge management
- Task management with YAML schemas
- Agent-to-agent communication
- Recursive self-improvement through failure tracking

See [`system-architecture/`](system-architecture/) for details.

### Bonus

[`bonus/the-day-i-met-onyx.md`](bonus/the-day-i-met-onyx.md) — A conversation transcript from the day our first AI agent chose its own name and wrote its own identity document. Included as something we found interesting about AI collaboration, not as documentation or a recommendation.

## Repository Structure

```
ai-development-workflows/
│
│  Developer Toolkit (drop into your project)
├── CLAUDE.md / AGENTS.md          # Entry points for AI assistants
├── documentation/                 # Project context template
├── rules-for-agents/              # Coding rules, workflows, API docs
│   ├── agentic-workflows/         #   PRD → Planning → Builder
│   ├── coding/                    #   Angular, Fastify, refactoring rules
│   └── api-documentation/         #   OpenAPI standards
│
│  From Our Production System
├── real-world-examples/           # Rules, commands, procedures, examples
│   ├── agent-rules.md             #   How we configure our dev agent
│   ├── commands/                  #   Quality review prompts
│   ├── procedures/                #   Feature exploration + development
│   └── task-lifecycle-example/    #   Real feature: discovery → delivery
│
├── system-architecture/           # Multi-agent system design insights
│   ├── multi-agent-architecture.md
│   ├── memory-system.md
│   ├── task-management.md
│   ├── agent-communication.md
│   └── self-improvement.md
│
└── bonus/
    └── the-day-i-met-onyx.md      # When our AI agent chose its name
```

## License

MIT — see [LICENSE](LICENSE).

## Attribution

If you use these workflows, we'd appreciate a link back to this repository.

---

**Built by [Snowit](https://snowit.sk)** — Refined through 2+ years of AI-assisted development on production projects.
