# API Documentation Overview

Quick reference for working with REST API documentation in your project.

<critical>
- API documentation is written in OpenAPI 3.0.3 format using modular YAML files
- Documentation is created BEFORE implementation (design phase)
- Directory structure mirrors URL paths exactly
- All endpoints must be documented following these conventions
</critical>

## When to Use These Rules

**Read [100-reading-api-docs.md](./100-reading-api-docs.md) when:**
- Understanding existing API endpoints
- Finding what parameters/responses an endpoint uses
- Learning how authentication works
- Reviewing API structure before implementing features

**Read [200-writing-api-docs.md](./200-writing-api-docs.md) when:**
- Designing new API endpoints
- Documenting endpoint specifications before coding
- Creating reusable components (parameters, schemas)
- Updating existing endpoint documentation

## Example Project Structure

```
your-project/
├── documentation/
│   └── rest-api/          # OpenAPI documentation
│       ├── openapi.yaml   # Main entry point
│       ├── components/    # Reusable elements
│       │   ├── parameters/
│       │   ├── schemas/
│       │   └── headers/
│       └── v1/           # API version 1
│           ├── auth/
│           ├── users/
│           └── resources/
```

## Core Principle: Directory Structure = URL Path

The file system **exactly mirrors** the API URL structure:

| API Endpoint | File Path |
|--------------|-----------|
| `POST /v1/auth/login` | `v1/auth/login/POST.yaml` |
| `GET /v1/scenarios` | `v1/scenarios/GET.yaml` |
| `GET /v1/scenarios/{scenarioId}` | `v1/scenarios/{scenarioId}/GET-PATCH-DELETE.yaml` |

## File Naming Convention

**Format:** `{METHOD}.yaml` or `{METHOD1-METHOD2}.yaml`

- Use **UPPERCASE** for HTTP methods (GET, POST, PATCH, DELETE)
- Combine multiple methods in one file when they share the same path
- Order: GET-POST-PATCH-DELETE

**Examples:**
- Single method: `GET.yaml`, `POST.yaml`
- Multiple methods: `GET-POST.yaml`, `GET-PATCH-DELETE.yaml`

## Standard Response Structure

Every response follows this pattern:

```json
{
  "meta": {
    "status": 200,
    "totalCount": 23,      // For lists only
    "pageSize": 100,       // For lists only
    "pageNumber": 1        // For lists only
  },
  "data": {
    // ... actual response data
  }
}
```

Errors use `error` instead of `data`:

```json
{
  "meta": { "status": 400 },
  "error": {
    "code": 0,
    "type": "client error",
    "message": "Error description",
    "errors": []  // Optional validation errors
  }
}
```

## Fastify Integration

### Workflow

1. **Design phase:** Write OpenAPI YAML files (document endpoints)
2. **Implementation phase:** Create Fastify routes with schemas
3. **Validation:** Fastify validates requests against schemas
4. **Documentation:** Serve OpenAPI files as interactive docs

### Using OpenAPI with Fastify

**Request validation in Fastify:**
```typescript
// Reuse OpenAPI schema structure in Fastify
app.post('/v1/scenarios', {
  schema: {
    body: {
      type: 'object',
      required: ['title', 'methodToTrain'],
      properties: {
        title: { type: 'string', minLength: 1 },
        methodToTrain: { type: 'string' }
      }
    }
  }
}, async (request, reply) => {
  // Request is automatically validated
});
```

**Serving documentation:**
```typescript
// Use @fastify/swagger to serve OpenAPI docs
import swagger from '@fastify/swagger';
import swaggerUi from '@fastify/swagger-ui';

await app.register(swagger, {
  mode: 'static',
  specification: {
    path: './docs/rest-api/openapi.yaml'
  }
});

await app.register(swaggerUi, {
  routePrefix: '/documentation'
});
// Documentation available at: http://localhost:3000/documentation
```

## Common Patterns

### CRUD Operations
- **List:** `GET /v1/resource` → Array in `data`
- **Get:** `GET /v1/resource/{id}` → Object in `data`
- **Create:** `POST /v1/resource` → Status `201`, created object
- **Update:** `PATCH /v1/resource/{id}` → Updated object
- **Delete:** `DELETE /v1/resource/{id}` → Status `200` or `204`

### Authentication
- **Login:** `POST /v1/auth/login` (anonymous) → Returns tokens
- **Authenticated requests:** Include `Authorization: Bearer {token}` header
- **Refresh:** `POST /v1/auth/refresh` → New tokens

## Naming Conventions

### Date/Time Properties
- `{name}Datetime` → ISO 8601 with timezone: `2020-10-19T13:28:06.419Z`
- `{name}Date` → Date only: `1980-05-21`
- `{name}Duration` → Milliseconds (number)
- `{name}Time` → Time of day in milliseconds from midnight (number)

### Property Naming
- Use **camelCase** for all properties
- Be descriptive: `firstName`, `createdAtDatetime`, `accessTokenExpiresInDuration`
- Use UUIDs with `format: uuid` for IDs

## Status Symbols

Use in `summary` field to indicate implementation status:

- **🟡 TODO** - Designed but not implemented yet
- **🟠 WIP** - Work in progress
- **🔴 ERROR** - Not working correctly
- **🔘 DESCOPED** - Will not be implemented
- **⚠️** - Warning or important note

**Example:**
```yaml
summary: Create scenario 🟡
```

## Viewing Documentation

**Best tool:** ReDoc Viewer (VS Code extension)

1. Open `documentation/rest-api/openapi.yaml`
2. Press `F1` (or `Cmd+Shift+P`)
3. Select: **"OpenAPI: show preview using ReDoc"**

This renders interactive documentation with search, collapsible sections, and examples.

## Quick Reference

### Reusable Components
- `Authorization` → Bearer token header
- `ContentTypeJson` → Content-Type: application/json
- `PageSize`, `PageNumber` → Pagination parameters
- `Error400`, `Error401`, `Error403`, `Error404` → Standard error responses

### Meta Schemas
- `MetaItemResponse` → Single item (GET by ID, POST, PATCH)
- `MetaListResponse` → List with pagination (GET collection)
- `MetaCreated` → Creation response

### Standard HTTP Status Codes
- `200` OK, `201` Created, `204` No Content
- `400` Bad Request, `401` Unauthorized, `403` Forbidden, `404` Not Found
- `412` Precondition Failed (conflict resolution)
