# Reading REST API Documentation

Guide for understanding and navigating existing API endpoint documentation.

<critical>
- Main entry point: `documentation/rest-api/openapi.yaml`
- Use ReDoc Viewer (VS Code extension) for best viewing experience
- Directory structure mirrors URL paths exactly
- Follow `$ref` links to find component definitions
</critical>

## Viewing Documentation

### ReDoc Viewer (Recommended)

1. Open `documentation/rest-api/openapi.yaml`
2. Press `F1` (or `Cmd+Shift+P`)
3. Select: **"OpenAPI: show preview using ReDoc"**

Provides:
- Collapsible sections
- Search functionality
- Request/response examples
- Inline component resolution

## Documentation Structure

### Main File: `openapi.yaml`

Contains:
- API metadata (title, version, servers)
- Global security configuration (JWT Bearer auth)
- Tags (endpoint groupings)
- Path references (links to endpoint files)
- Component references (reusable elements)

**What to find here:**
- List of all available endpoints
- API conventions and standards
- General authentication requirements

### Endpoint Files: `v1/{resource}/{...}/{METHOD}.yaml`

Pattern: `v1/{resource}/{subresource}/{...}/[{parameter}]/{METHOD}.yaml`

**Examples:**
- `v1/auth/login/POST.yaml` → `POST /v1/auth/login`
- `v1/scenarios/GET-POST.yaml` → `GET /v1/scenarios` and `POST /v1/scenarios`
- `v1/scenarios/{scenarioId}/GET-PATCH-DELETE.yaml` → Three methods on same path

### Component Files: `components/{category}/{name}.yaml`

```
components/
├── parameters/      # Path, query, header parameters
├── schemas/         # Data structures and error responses
├── headers/         # Response headers
└── security-schemes/ # Authentication schemes
```

**File naming:** lowercase-with-dashes (e.g., `scenario-id.yaml`)

## Finding What You Need

### Finding an Endpoint

**Method 1: From main file**
1. Open `openapi.yaml`
2. Find `paths:` section
3. Locate endpoint path (e.g., `/v1/auth/login`)
4. Follow the `$ref` link

**Method 2: Direct navigation**
1. Identify URL path: `/v1/scenarios/{scenarioId}`
2. Navigate to: `v1/scenarios/{scenarioId}/`
3. Open file matching HTTP method (e.g., `GET-PATCH-DELETE.yaml`)

### Finding Request/Response Structure

In endpoint file, look for:

**Request:**
- `parameters:` → Query params, path params, headers
- `requestBody:` → Request payload structure

**Response:**
- `responses:` → Status codes (200, 201, 400, 401, etc.)
- Each response contains `schema:` with data structure

### Finding Reusable Components

**From endpoint files:**
Look for `$ref` links like:
```yaml
$ref: '../../../openapi.yaml#/components/parameters/ScenarioId'
```

This references `components/parameters/scenario-id.yaml`

**Direct access:**
Navigate to `components/{category}/` and browse files.

## Understanding Endpoint Documentation

### Anatomy of an Endpoint File

```yaml
{method}:                           # e.g., get, post, patch, delete
  operationId: kebab-case-name      # Unique identifier
  summary: Short description        # Brief title
  description: |                    # Full markdown description
    <small>**Target Roles:**</small> `admin`, `customer`

    Detailed explanation...

  tags:
    - Authentication                # Groups endpoints in viewer

  security: []                      # [] = anonymous, omit = requires Bearer token

  parameters:                       # Request parameters
    - $ref: 'path/to/parameter.yaml'

  requestBody:                      # Request payload (POST, PATCH, PUT)
    required: true
    content:
      application/json:
        schema:
          type: object
          required: [field1]
          properties:
            field1:
              type: string
              description: Field description
              example: 'example value'

  responses:                        # Response definitions
    '200':
      description: OK
      x-summary: Extended summary
      content:
        application/json:
          schema:
            type: object
            properties:
              meta:
                $ref: 'path/to/meta-schema.yaml'
              data:
                type: object
                # ... response data structure
          examples:                 # Concrete examples
            success:
              value:
                meta: { status: 200 }
                data: { ... }
    '400':
      $ref: 'path/to/error-400.yaml'
```

### Key Sections

#### Target Roles

Indicates who can access the endpoint:

```yaml
description: |
  <small>**Target Roles:**</small> `admin`, `customer`
```

**Notation:**
- `anonymous` → No authentication required
- `admin`, `customer` → Specific roles
- `>` → "at least" (e.g., `customer > contributor`)

#### Security

```yaml
security: []        # Anonymous access (overrides global auth)
# (omitted)        # Requires Bearer token (default)
```

#### Parameters

**Common reusable parameters:**
- `Authorization` → Bearer token header
- `ContentTypeJson` → Content-Type: application/json
- `PageSize`, `PageNumber` → Pagination
- `ScenarioId`, `ConversationId` → Path parameters

**Path parameters** are in URL: `/scenarios/{scenarioId}`
**Query parameters** follow `?`: `/scenarios?pageSize=50`

#### Response Structure

**Success response:**
```json
{
  "meta": {
    "status": 200,
    "totalCount": 23,      // Lists only
    "pageSize": 100,       // Lists only
    "pageNumber": 1        // Lists only
  },
  "data": {
    // ... actual response data
  }
}
```

**Error response:**
```json
{
  "meta": { "status": 400 },
  "error": {
    "code": 0,
    "type": "client error",
    "message": "Error description",
    "errors": [            // Optional validation errors
      {
        "code": 0,
        "property": "payload.title",
        "message": "'title' is required"
      }
    ]
  }
}
```

#### Examples

Many responses include concrete examples:
```yaml
examples:
  success:
    summary: Successful login
    value:
      meta: { status: 200 }
      data: { accessToken: "eyJ..." }
```

## Status Symbols

In `summary` field:

- **🟡 TODO** → Designed but not implemented
- **🟠 WIP** → Work in progress
- **🔴 ERROR** → Not working correctly
- **🔘 DESCOPED** → Will not be implemented
- **⚠️** → Warning or important note

**Example:**
```yaml
summary: Create scenario 🟡
```

## Conventions

### Pagination

List endpoints use:

**Parameters:**
- `pageSize` → Items per page (default: 100, max: 250)
- `pageNumber` → Page number (starts at 1)

**Response meta:**
```json
{
  "meta": {
    "status": 200,
    "totalCount": 523,
    "pageSize": 100,
    "pageNumber": 1
  }
}
```

### Sorting

Format: `?sort=property` or `?sort=-property` (descending)

**Examples:**
- `?sort=created` → Ascending by creation date
- `?sort=-created` → Descending by creation date
- `?sort=-created,title` → Multiple fields

### Filtering

Format: `?filter[property]=value`

**Example:** `?filter[status]=active`

## Common Patterns

### Authentication Flow

1. **Login:** `POST /v1/auth/login` (anonymous)
   - Returns `accessToken` and `refreshToken`
2. **Authenticated requests:** Include header:
   ```
   Authorization: Bearer {accessToken}
   ```
3. **Token refresh:** `POST /v1/auth/refresh`
4. **Logout:** `POST /v1/auth/logout`

### CRUD Operations

- **List:** `GET /v1/resource` → Array in `data`
- **Get:** `GET /v1/resource/{id}` → Object in `data`
- **Create:** `POST /v1/resource` → Created object, status `201`
- **Update:** `PATCH /v1/resource/{id}` → Updated object
- **Delete:** `DELETE /v1/resource/{id}` → Status `204` or `200`

### Nested Resources

Resources can be nested:

- `GET /v1/scenarios/{scenarioId}/conversations`
- `POST /v1/scenarios/{scenarioId}/conversations`
- `PATCH /v1/scenarios/{scenarioId}/conversations/{conversationId}`

### Error Handling

All endpoints document standard errors:

- **400 Bad Request** → Invalid input, validation errors
- **401 Unauthorized** → Missing or invalid authentication
- **403 Forbidden** → Authenticated but not authorized
- **404 Not Found** → Resource doesn't exist

Each error response includes:
- HTTP status code (also in `meta.status`)
- Error code, type, and message
- Optional validation errors array

## Relative Path Reference

The number of `../` needed depends on directory depth:

| Depth | Example Path | Relative Path to openapi.yaml |
|-------|--------------|-------------------------------|
| 2 | `v1/auth/` | `../../openapi.yaml` |
| 3 | `v1/auth/login/` | `../../../openapi.yaml` |
| 4 | `v1/scenarios/{scenarioId}/` | `../../../../openapi.yaml` |

**Tip:** Count slashes in file path from `v1/` to determine depth.

## Troubleshooting

### Can't find an endpoint?

1. Check `openapi.yaml` `paths:` section
2. Look for exact path (including `/v1/` prefix)
3. Follow the `$ref` to the file

### Schema seems incomplete?

1. Look for `$ref` links to component files
2. Check `components/schemas/` for full definitions
3. Use ReDoc viewer to see complete merged view

### Parameter not documented?

1. Check if it's a reusable parameter (linked via `$ref`)
2. Look in `components/parameters/` directory
3. Common ones: `Authorization`, `PageSize`, `PageNumber`

### Response structure unclear?

1. Look for `examples:` section in response
2. Check referenced schemas in `components/schemas/`
3. Verify `meta` structure (always present)

## Quick Reference

### Standard Responses
```json
// Success (item)
{ "meta": { "status": 200 }, "data": { /* item */ } }

// Success (list)
{ "meta": { "status": 200, "totalCount": 10, "pageSize": 100, "pageNumber": 1 }, "data": [ /* items */ ] }

// Error
{ "meta": { "status": 400 }, "error": { "code": 0, "type": "client error", "message": "...", "errors": [] } }
```

### Common Parameters
- `Authorization` → Bearer token
- `ContentTypeJson` → JSON content type
- `PageSize`, `PageNumber` → Pagination
- `ScenarioId`, `ConversationId`, `UserId` → Path parameters

### Date/Time Conventions
- `createdAtDatetime` → ISO 8601: `2020-10-19T13:28:06.419Z`
- `birthdayDate` → Date: `1980-05-21`
- `accessTokenExpiresInDuration` → Milliseconds
- `wokeUpTime` → Milliseconds from midnight
