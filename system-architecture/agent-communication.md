# Agent Communication

## Overview

Our agents communicate through three mechanisms: peer messaging (agent-to-agent), self-spawn (creating new sessions), and deferred actions (scheduling future work). All three use Slack as the transport layer — the orchestrator handles the routing.

## Peer Messaging

Agents can send messages to each other by posting to Slack channels with an @mention of the target agent's bot. The orchestrator routes the message to the right workspace.

### How It Works

1. Agent A posts a message mentioning Agent B's bot user ID in a channel
2. The orchestrator routes it to Agent B's workspace
3. Agent B receives the message with a system prompt note: "Sender: AgentA (agent-a-bot, peer agent)"
4. Agent B responds in the thread (no @mention back unless it needs something)

### Depth Cap

To prevent cost amplification (agent A asks agent B, which asks agent C, which asks agent D...), there's a **max 2 agent hops** per thread. The orchestrator tracks hop depth and refuses to route messages that exceed it.

### Protocol

Agents must read the peer-messaging protocol before sending their first message in a session. This ensures they use the correct bot user IDs and channel targets. The protocol includes a registry of all agents with their names, bot names, and channels.

## Self-Spawn

An agent can create a new session for itself in a fresh Slack thread. This is useful for separable subtasks, parallel investigations, or long-running work that shouldn't block the current conversation.

### How It Works

1. Agent posts a message with a `[new-thread]` prefix to an appropriate channel
2. The orchestrator strips the marker and starts a new Claude Code session
3. The new session gets a fresh context window with a link back to the parent thread

### Safeguards

- **Cap**: Maximum 5 unacknowledged spawned threads per workspace. A human reply acknowledges and frees capacity.
- **Channel restriction**: Only works in channels where the workspace has a `require_mention: false` route.
- **Ref lines**: Each spawned thread includes a machine-readable reference line (`ref:workspace:session-id:origin-thread`) so follow-up sessions can trace the chain.

## Self-Resume

An agent can re-activate an existing thread it previously created by posting a `[resume]` marker as a thread reply. This resumes the Claude Code session with full prior context.

### When To Use

When new information arrives for an existing thread (e.g., a follow-up email related to an already-analyzed topic). Instead of spawning a duplicate, the agent injects the new information into the original thread.

### Safeguards

- **Cooldown**: 5-minute per-thread cooldown to prevent rapid-fire resumes
- **Cap**: Counts against the same self-spawn cap

## Deferred Actions

Agents can schedule one-shot future work using a scheduling tool. This creates durable actions that survive session restarts — unlike in-session timers, which die when the session ends.

### How It Works

```
schedule create \
  --delay 15m \
  --message "Check if the deployment succeeded" \
  --reply-to <channel>:<thread-ts> \
  --context-snapshot 5
```

The orchestrator stores the action and fires it as a fresh session when the delay expires. The `--context-snapshot` flag attaches recent Slack messages from the target thread, so the new session has context.

### Use Cases

- **Reminders**: "Check back in 2 hours if we got a response"
- **Verification**: "After deployment, verify the fix works"
- **Follow-ups**: "If no reply by end of day, escalate"

Maximum delay: 24 hours. For longer scheduling, tasks with due dates are more appropriate.

## Cross-Session Context

A convention for maintaining context across threads and sessions:

**Ref lines**: Machine-readable references appended to proactive messages:
```
ref:workspace-name:session-8chars:origin-thread-ts
```

When a new session starts in a thread with a ref line, it can:
1. Find the original session from the daily log using the session ID
2. Fetch context using session history or thread history
3. Continue the work with full background

**Memory trails**: For interactive proactive messages, the agent stores a memory entry linking the new thread to the original context.

## What We Learned

- **Fire-and-forget is the right default.** Agents can't wait for replies in the same session (the session may have already ended). Designing around this constraint makes the system more robust — each agent handles its own domain independently.

- **Depth capping is essential.** Without it, a simple question can cascade through multiple agents, each spawning sub-agents, consuming significant resources. Two hops handles all legitimate use cases.

- **Self-spawn needs caps.** Without limits, agents would spawn threads for everything. The cap forces them to be selective and reuse existing threads.

- **Deferred actions replaced cron for one-off scheduling.** Cron jobs are static and defined in config. Deferred actions are dynamic and created by agents at runtime. Both serve different purposes — cron for recurring patterns, deferred actions for situational follow-ups.
