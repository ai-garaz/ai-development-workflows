# Backend Essentials (Node.js + Fastify + TypeScript)

Rules for backend development focusing on common pitfalls and anti-patterns.

<critical>
- WHENEVER YOU'RE USING THESE RULES, always include the following in the first line: "BASED ON BACKEND RULES"
- Backend uses Node.js 20+ with TypeScript in ESM mode
- Fastify 5.x is the web framework
</critical>

## Project Structure

- Routes organized by resource/domain (e.g., `routes/scenarios/`, `routes/conversations/`)
- Business logic in service layer, not in route handlers
- Shared utilities in `src/utils/`
- Type definitions and interfaces co-located with implementation or in `src/types/`

## ESM Module System (Critical)

<critical>
- **NEVER use** `require()` or `module.exports` - this is CommonJS
- **ALWAYS use** `import` and `export`
- **ALWAYS include** `.js` extensions in relative imports (TypeScript + Node.js ESM requirement)
</critical>

```typescript
// ✅ CORRECT - ESM with .js extensions
import { FastifyInstance } from 'fastify';
import { createScenario } from '../services/scenarios.js';  // .js extension required
import type { User } from '../types/user.js';

// ❌ WRONG - CommonJS
const fastify = require('fastify');

// ❌ WRONG - Missing .js extension
import { createScenario } from '../services/scenarios';
```

## Error Handling in Routes (Critical)

<critical>
- **ALWAYS wrap async route handlers in try-catch**
- Unhandled errors crash the entire server
- Return proper HTTP status codes
- **NEVER log stack traces in production** - only in development (check `config.env`)
</critical>

```typescript
// ✅ CORRECT
app.get('/scenarios/:id', async (request, reply) => {
  try {
    const scenario = await getScenario(request.params.id);
    if (!scenario) {
      return reply.status(404).send({ error: 'Not found' });
    }
    return reply.send({ data: scenario });
  } catch (error) {
    request.log.error(error);
    return reply.status(500).send({ error: 'Internal server error' });
  }
});

// ❌ WRONG - No error handling (crashes server)
app.get('/scenarios/:id', async (request, reply) => {
  const scenario = await getScenario(request.params.id);
  return reply.send({ data: scenario });
});
```

## Input Validation (Critical)

<critical>
- **ALWAYS validate** request bodies, params, and query strings
- Use Fastify schemas for validation
- Prevents security issues and bugs
</critical>

```typescript
// ✅ CORRECT - Schema validation
app.post('/scenarios', {
  schema: {
    body: {
      type: 'object',
      required: ['title'],
      properties: {
        title: { type: 'string', minLength: 1, maxLength: 200 },
        userId: { type: 'string', format: 'uuid' }
      },
      additionalProperties: false  // Reject unexpected fields
    }
  }
}, async (request, reply) => {
  const scenario = await createScenario(request.body);
  return reply.status(201).send({ data: scenario });
});
```

## WebSocket Cleanup (Critical)

<critical>
- **ALWAYS handle** close and error events
- **ALWAYS clean up** on disconnect (remove listeners, clear maps/sets)
- Missing cleanup causes memory leaks
</critical>

```typescript
// ✅ CORRECT - Proper cleanup
app.get('/ws/:conversationId', { websocket: true }, (connection, request) => {
  activeConnections.set(connection.socket, request.params.conversationId);

  connection.socket.on('message', async (data) => {
    try {
      await handleMessage(data);
    } catch (error) {
      request.log.error(error);
    }
  });

  // CRITICAL - Cleanup on close
  connection.socket.on('close', () => {
    activeConnections.delete(connection.socket);
  });

  // CRITICAL - Cleanup on error
  connection.socket.on('error', (error) => {
    request.log.error(error);
    activeConnections.delete(connection.socket);
  });
});

// ❌ WRONG - No cleanup (memory leak)
app.get('/ws/:conversationId', { websocket: true }, (connection) => {
  connection.socket.on('message', handleMessage);
  // Missing: close handler, error handler = memory leak
});
```

## Security Essentials

### API Keys & Secrets

<critical>
- **NEVER log** API keys or secrets
- Validate required env vars at startup (fail fast)
- Never expose service keys to frontend
</critical>

### Timeouts

<critical>
- **ALWAYS set timeouts** for external API calls
- Prevents hanging requests and resource exhaustion
</critical>

```typescript
// ✅ CORRECT - With timeout
const response = await fetch(url, {
  signal: AbortSignal.timeout(30000)  // 30 second timeout
});

// ❌ WRONG - No timeout (can hang forever)
const response = await fetch(url);
```

### CORS Configuration

- Use explicit origins in production (never `origin: '*'`)
- Set appropriate `credentials` setting
- Consider using environment variables for allowed origins

### Rate Limiting

- Implement rate limiting for API endpoints
- Use stricter limits for expensive operations (AI, file uploads)
- Fastify plugin: `@fastify/rate-limit`

## Supabase Patterns

<critical>
- **Always question the need to use service key** - it bypasses RLS security
- **Always prefer user client** (anon key + user JWT) unless absolutely necessary
- Service key should only be used for: system operations, migrations, cross-user analytics
- Never expose service key to frontend
</critical>

## Resource Cleanup

<critical>
- **ALWAYS clean up** streams, connections, and timers
- Implement graceful shutdown (handle SIGTERM, SIGINT)
- Close WebSocket connections, destroy streams, clear intervals/timeouts
- Call `app.close()` to properly shut down Fastify
</critical>

## Streaming & Backpressure

<critical>
- **Handle backpressure** when streaming data
- Pause input stream if buffer grows too large
- Resume after processing
- Prevents memory exhaustion
</critical>

Pattern: Track buffer size → pause if too large → process → resume

## Error Handling Patterns

- Create custom error classes with status codes
- Use Fastify's `setErrorHandler` for centralized error handling
- Don't expose internal error details to clients
- Log errors with structured context (user ID, request ID, etc.)

## Logging

- **ALWAYS use `request.log`** (not `console.log`)
- Use structured logging with context objects
- Log levels: `info`, `warn`, `error`, `debug`
- Include relevant IDs (user, conversation, request)

## Database Migrations (Supabase)

<critical>
- **ALWAYS create migrations** for database schema changes
- **ALWAYS place migrations** in `backend/supabase/migrations/`
- **NEVER modify existing migrations** - create new ones instead
- **ALWAYS test migrations locally** before committing
</critical>

### Migration Guidelines

**File Naming:**
```
backend/supabase/migrations/
├── 001_create_scenarios_table.sql
├── 002_create_email_whitelist_table.sql
└── 003_add_column_to_scenarios.sql
```

Pattern: `###_descriptive_name.sql` (3 digits, snake_case)

**Creating Migrations:**
```bash
# Manual approach (recommended for full control)
cd backend
# Create file: supabase/migrations/###_descriptive_name.sql
# Write SQL (CREATE TABLE, ALTER TABLE, etc.)
# Test locally
npx supabase migration up --local

# Verify in Supabase Studio
# http://localhost:54323
```

**Testing Migrations:**
```bash
# Apply to local DB
cd backend
npx supabase migration up --local

# If issues, fix the SQL file and reset
npx supabase db reset --local  # Resets and reapplies all migrations
```

**Migration Best Practices:**
- Use transactions (wrap in `BEGIN; ... COMMIT;`)
- Add `IF NOT EXISTS` checks where appropriate
- Include rollback instructions in comments
- Test both up and down migrations
- Keep migrations idempotent when possible

## Testing

### Test Configuration & Constants

- **ALWAYS centralize test constants** in `src/test/constants.ts`
- **NEVER hardcode test emails, passwords, or magic numbers** in test files
- Define: `TEST_EMAILS`, `TEST_PASSWORDS`, `PAGINATION` constants

### Test User Management & Cleanup

<critical>
- **Use whitelisted test emails** from migration (defined in `TEST_EMAILS`)
- **Tests run sequentially** (`fileParallelism: false` in vitest.config.ts) to prevent conflicts
- **Each test creates and deletes users** in beforeAll/afterAll hooks
- **ALWAYS clean up ALL created resources** in afterAll - use DELETE endpoints, not just CASCADE
- **Database should be empty after test suite** - verify with manual DB checks
</critical>

### Test Helpers

Available in `src/test/helpers/`:
- `createTestUser(email, password)` - Creates user, returns userId + accessToken
- `deleteTestUser(userId)` - Removes user from Supabase Auth
- `createApiClient(app)` - Creates supertest instance for HTTP testing
- `signInTestUser(email, password)` - Gets token for existing user

## API Documentation

<critical>
- All REST endpoints must be documented following OpenAPI 3.0.3 conventions
- Documentation is created BEFORE implementation (design phase)
- UPDATE documentation when adding missing endpoints
- See [../api-documentation/](../api-documentation/) for detailed guides
</critical>

**Quick reference:**
- [000-overview.md](../api-documentation/000-overview.md) - Start here
- [100-reading-api-docs.md](../api-documentation/100-reading-api-docs.md) - Understanding existing endpoints
- [200-writing-api-docs.md](../api-documentation/200-writing-api-docs.md) - Documenting new endpoints

**Integration with Fastify:**
- Reuse OpenAPI schemas in Fastify route validation
- Serve OpenAPI docs using `@fastify/swagger` and `@fastify/swagger-ui`
- Keep OpenAPI YAML as source of truth
