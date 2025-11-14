# AI Development Workflows

A collection of structured prompts and coding rules for AI-assisted software development, designed to help development teams work more effectively with AI coding assistants like Claude Code, GitHub Copilot, Codex, and Gemini.

## What's Inside

This repository contains battle-tested workflows and coding standards developed through real-world project experience:

- **Agent Workflows** — Structured prompts for PRD creation, planning, and implementation
- **Coding Rules** — Language and framework-specific best practices
- **API Documentation Standards** — Guidelines for reading and writing API documentation

## Tech Stack

These workflows are **optimized for**:
- **Frontend**: Angular 20+ with PrimeNG 20+
- **Backend**: Fastify (TypeScript) with Node.js ESM modules
- **API**: REST with OpenAPI 3.0.3 documentation

However, the **core workflow patterns** (PRD → Planning → Building) are framework-agnostic and can be adapted to any stack.

## Quick Start

Simply extract the contents into any project directory (we recommend starting with a test project). The included files will automatically be recognized by:

- **Claude Code** — via `CLAUDE.md`
- **Codex, Gemini, GitHub Copilot** — via `AGENTS.md`

Both files contain the same instructions optimized for different AI assistants.

### Important: First-Time Setup

After copying files to your project:

1. **Create `documentation/about-project.md`** with your project details (see template below)
2. **Optional**: Update tech stack references in `rules-for-agents/coding/001-general-coding-rules.md` if you're not using Angular/Fastify

**Note**: The workflows use generic terminology like "this project" and "this feature" - you can use them as-is without needing to find-and-replace project names.

**about-project.md template:**
```markdown
# [Your Project Name]

## Overview
Brief description of what your project does.

## Tech Stack
- Frontend: [Your framework]
- Backend: [Your framework]
- Database: [Your database]

## Project Goals
What you're building and why.
```

## How to Use

1. Copy the contents to your project root
2. Claude Code will automatically detect `CLAUDE.md`, other agents should automatically detect `AGENTS.md` and follow the workflows
3. Try commands like:
   - "Create a PRD for [feature]"
   - "Plan the implementation"
   - "Build the feature"


## Structure

```
workflow-prompts/
├── CLAUDE.md                    # Main entry point for Claude Code
├── AGENTS.md                    # Main entry point for other AI assistants
└── rules-for-agents/
    ├── agentic-workflows/       # PRD, Planning, and Builder workflows
    ├── api-documentation/       # API documentation standards
    └── coding/                  # Coding rules and best practices
```

## Customization

These workflows are templates designed to be adapted. Here's what to customize:

### Essential Changes

1. **Tech Stack** — Update `rules-for-agents/coding/001-general-coding-rules.md` to reflect your stack
2. **Project Context** — Create `documentation/about-project.md` with your project details
3. **Coding Rules** — Modify files in `rules-for-agents/coding/` to match your conventions
4. **API Standards** — Adapt `rules-for-agents/api-documentation/` if you use different API patterns

### Optional Customizations

- Adjust workflow phases to fit your team's process
- Add project-specific requirements and constraints
- Remove sections that don't apply (e.g., API docs if you're not building APIs)
- Extend with additional rules for your specific domain

## Contributing

Found ways to improve these workflows? Contributions are welcome! Please open an issue or pull request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Attribution

If you use these workflows, please link back to this repository. We'd love to hear how they're working for you!

## Questions?

Open an issue in this repository for questions, suggestions, or to share your experience using these workflows.

---

**Built by [Snowit](https://snowit.sk)** — Refined through 2+ years of AI-assisted development on production projects.
