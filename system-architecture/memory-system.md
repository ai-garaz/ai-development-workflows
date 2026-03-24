# Memory System

## The Problem

Each Claude Code session starts with a fresh context window. Without a memory system, agents lose everything they learned in previous sessions — decisions, preferences, domain knowledge, what was tried and failed.

## Our Approach

A multi-source indexed memory system that agents query via a CLI tool. The core principle: **Markdown is canonical, the index is derived.** All knowledge lives in plain text files that agents (and humans) can read directly. The index makes it searchable but is never the source of truth — it can be rebuilt from the files at any time.

## Memory Sources

| Source | What it contains | Updated by |
|--------|-----------------|------------|
| **MEMORY.md** | Universal knowledge — facts, preferences, decisions, learnings | Agents (via `memory store`) |
| **Daily logs** | Session registry with timestamps, summaries, and what happened | Agents (after each session) |
| **Knowledge files** | Domain-specific reference material (organized in `knowledge/`) | Agents + humans |
| **Task files** | Work items with activity logs tracking decisions and progress | Agents |
| **Transcripts** | Full session histories for deep context retrieval | Automated indexing |

## How Search Works

**Hybrid retrieval**: vector similarity + keyword (FTS5) search. Neither alone is sufficient — vector search finds semantically related content but misses exact terms; keyword search finds exact matches but misses paraphrased concepts. The combination covers both.

**Technical stack**: SQLite + sqlite-vec for vector storage, FTS5 for full-text search. Heading-based chunking with ~400 token target and 80 token overlap.

## When Agents Search

Agents are instructed to search memory proactively in these situations:

1. **Session start** — 1-2 queries from the incoming message (core topic + named entities)
2. **Topic shift** — new subject not yet searched this session
3. **Named entities** — people, projects, tools mentioned for the first time
4. **Before proposing decisions** — check for prior decisions on the same topic
5. **Contradictions or uncertainty** — verify rather than trusting stale assumptions

And explicitly NOT to search when:
- The answer is already in the current session context
- They're doing straightforward file operations
- The operator gave all needed information

## What We Learned

- **Search before storing.** Duplicate memories degrade retrieval quality. Agents check if a fact already exists and update the existing entry rather than creating a new one.

- **Attribution matters.** We work with multiple operators. Memory entries tagged with `[Stano]` or `[Johnny]` let agents apply preferences and context to the right person. This prevents awkward misapplications.

- **MEMORY.md is searched, not pre-loaded.** Early on, we loaded the entire MEMORY.md into every session's context. It grew to 60KB+ and consumed too much of the context window. Now it's indexed and searched like everything else — agents only retrieve what's relevant to the current task.

- **"When did we decide X?" is the most valuable query type.** The memory system's highest-ROI use case is finding prior decisions. Without it, agents (and humans) revisit the same questions, sometimes reaching different conclusions.

- **Lazy and incremental indexing.** Content hashing means only changed files get re-indexed. Full reindex is available but rarely needed.
