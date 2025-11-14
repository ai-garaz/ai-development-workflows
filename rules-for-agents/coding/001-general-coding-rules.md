# General Coding Rules

Rules to be followed in every task involving code analysis, writing, debugging, planning, or refactoring.

<critical>
- This is a full-stack project with both frontend (Angular) and backend (Fastify) components
- Frontend uses Angular 20+ with PrimeNG 20+
- Backend uses Fastify (TypeScript) with Node.js ESM modules
- For frontend-specific tasks, check out [002-frontend-angular-rules.md](002-frontend-angular-rules.md)
- For backend-specific tasks, check out [003-backend-fastify-rules.md](003-backend-fastify-rules.md)
</critical>

## Instructions

### Core Principles

- Apply immutability and pure functions where applicable
- Use declarative programming patterns
- Optimize for readability and maintainability of code
- Ideally keep TypeScript files under 500 lines
- Ideally keep HTML files under 500 lines (frontend)
- Avoid naming functions based on what they react to; name them based on what they do
- Prioritize type safety - leverage TypeScript's type system fully

### Code Structure

- Keep functions and components focused on a single responsibility
- Maintain reasonable DRY (Don't Repeat Yourself) principles
- Break complex functions/components/modules into smaller, reusable pieces
- Keep business logic separate from infrastructure concerns

### TypeScript Standards

- Use explicit types for function parameters and return values
- Avoid `any` - use `unknown` if type is truly unknown
- Prefer interfaces for object shapes, types for unions/intersections
- Place interfaces that are likely to be used within one file only at the end of that file after the main functionality
- Place interfaces that are likely to be shared into separate `.types.ts` file
- Use jsdoc comments and keep them updated when making changes - be concise and minimize extraneous prose in comments

### Code Quality

- Write self-documenting code with clear variable and function names
- Add comments only when code intent isn't obvious - NEVER to explain why a line was added or function changed as such information holds no value in the long run
- Extract magic numbers and strings to named constants
- Handle errors explicitly - no silent failures
- Log meaningful messages with appropriate log levels

### Testing Readiness

- Write code that's easy to test
- Avoid tight coupling between modules
- Keep side effects isolated and explicit

### Performance Considerations

- Be mindful of memory usage, especially for long-running conversations
- Use streaming for large data transfers (audio, LLM responses)
- Implement proper cleanup for resources (WebSocket connections, streams, timers)
- Consider lazy loading for features not needed at initial load

### Security

- Never commit API keys or secrets to version control
- Validate all user inputs
- Sanitize data before database operations
- Use Supabase RLS (Row Level Security) policies appropriately
- Follow principle of least privilege for API access

### Dependencies

- Keep dependencies up to date with security patches
- Document any version pins or overrides (e.g., `@supabase/auth-js` downgrade)
- Prefer well-maintained, popular libraries for critical functionality
- Minimize bundle size - avoid heavy dependencies for simple tasks
