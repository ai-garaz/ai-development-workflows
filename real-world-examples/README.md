# Real-World Examples

These are the prompts that power our AI-first development workflow. They come from a production system where AI agents handle feature development — from initial exploration through code delivery.

We share them as practical reference, not as plug-and-play tools. They're shaped by our codebase, our tools, and our team. The value is in the patterns and thinking, not in copying the files verbatim.

## What's Here

### [Agent Rules](agent-rules.md)

A curated excerpt from the CLAUDE.md file we use for our development agent. Shows how we structure rules for an agent that works on a real codebase across multiple sessions — core principles, model selection guidance, workspace organization, and multi-language conventions.

### [Commands](commands/)

Prompts we invoke during development for quality assurance. Each command spawns parallel sub-agents that audit work from different angles:

| Command | Purpose |
|---------|---------|
| [review-solution](commands/review-solution.md) | Audit a proposed or implemented solution for completeness and failure points |
| [review-plan](commands/review-plan.md) | Audit a plan against the original request for coverage gaps |
| [review-execution](commands/review-execution.md) | Audit implementation against the plan for drift and missed requirements |
| [double-check](commands/double-check.md) | Adversarial pair review — one reviewer for correctness, one for holes |

### [Procedures](procedures/)

Step-by-step workflows for the two main phases of feature development:

| Procedure | Phase | What it does |
|-----------|-------|-------------|
| [explore-task](procedures/explore-task.md) | Discovery | Gather context, ask clarifying questions, produce PRD and implementation plan |
| [development](procedures/development.md) | Implementation | Execute the plan with quality gates, session continuity, and anti-patterns |

### [Task Lifecycle Example](task-lifecycle-example/)

A sanitized real feature showing the complete output of our workflow — from PRD through implementation to delivery. This is what the procedures above actually produce.

## How These Fit Together

```
1. Agent rules (CLAUDE.md)     — define the agent's persona, principles, and constraints
2. Explore-task procedure      — discover → analyze → PRD → implementation plan
3. Development procedure       — implement → quality gates → deliver
4. Commands                    — invoked during procedures for quality checks
```

The task lifecycle example shows the artifacts that steps 2-3 produce for a real feature.
