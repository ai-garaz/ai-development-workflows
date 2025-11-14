# Writing REST API Documentation

Step-by-step guide for documenting new API endpoints using OpenAPI 3.0.3.

<critical>
- Document endpoints BEFORE implementation (design phase)
- Directory structure must exactly mirror URL paths
- Use UPPERCASE for HTTP methods in filenames
- Always update main openapi.yaml after creating endpoint files
- Follow naming conventions strictly for consistency
</critical>

## Workflow

1. **Design phase:** Create OpenAPI YAML documentation
2. **Review phase:** Validate structure, check examples
3. **Implementation phase:** Build Fastify endpoint using documented schema
4. **Validation phase:** Ensure implementation matches documentation

## File Organization Rules

### Directory Structure = URL Path

The file structure **exactly mirrors** the API URL structure:

**Pattern:**
```
documentation/rest-api/v1/{resource}/{subresource}/{...}/[{parameter}]/{METHOD}.yaml
```

**Examples:**

| API Endpoint | File Path |
|--------------|-----------|
| `POST /v1/auth/login` | `v1/auth/login/POST.yaml` |
| `GET /v1/scenarios` | `v1/scenarios/GET.yaml` |
| `GET /v1/scenarios/{scenarioId}` | `v1/scenarios/{scenarioId}/GET-PATCH-DELETE.yaml` |

### File Naming Convention

**Format:** `{METHOD}.yaml` or `{METHOD1-METHOD2-METHOD3}.yaml`

**Rules:**
- Use **UPPERCASE** for HTTP methods (GET, POST, PATCH, DELETE)
- Combine multiple methods in one file when they share the same path
- Order methods: GET-POST-PATCH-DELETE

**Examples:**
- Single: `GET.yaml`, `POST.yaml`
- Multiple: `GET-POST.yaml`, `GET-PATCH-DELETE.yaml`

### Component File Naming

**Format:** `{name-with-dashes}.yaml` (lowercase kebab-case)

**Examples:**
- `scenario-id.yaml`
- `error-400-response.yaml`
- `last-modified.yaml`

## Step-by-Step: Creating a New Endpoint

### Step 1: Determine File Path

1. Identify full endpoint URL: `POST /v1/scenarios/{scenarioId}/conversations`
2. Remove `/v1/` prefix: `scenarios/{scenarioId}/conversations`
3. Navigate to: `documentation/rest-api/v1/scenarios/{scenarioId}/conversations/`
4. Check if file exists for this path
5. Create/update appropriate file

**Decision tree:**
```
Does the path directory exist?
├─ YES → Does a file exist for this path?
│        ├─ YES → Add new method to existing file
│        └─ NO → Create {METHOD}.yaml
└─ NO → Create directory structure + {METHOD}.yaml
```

### Step 2: Create Directory Structure (if needed)

```bash
cd documentation/rest-api/v1/scenarios/{scenarioId}/
mkdir conversations
cd conversations
```

### Step 3: Create Endpoint File

Use templates below based on operation type.

### Step 4: Update Main openapi.yaml

Add entry to `paths:` section:

```yaml
paths:
  /v1/scenarios/{scenarioId}/conversations:
    $ref: './v1/scenarios/{scenarioId}/conversations/POST.yaml'
```

## Endpoint File Templates

### Template: List Endpoint (GET Collection)

```yaml
get:
  operationId: get-{resource}-list
  summary: List of {resources}
  description: |
    <small>**Target Roles:**</small> `{role}`

    Returns paginated list of {resources}.

  tags:
    - {Tag}

  parameters:
    - $ref: '../../openapi.yaml#/components/parameters/Authorization'
    - $ref: '../../openapi.yaml#/components/parameters/PageSize'
    - $ref: '../../openapi.yaml#/components/parameters/PageNumber'
    - name: sort
      in: query
      required: false
      description: |
        Sorting criteria:
        - `created` - creation datetime (default)
        - `title` - alphabetical
      schema:
        type: string
        enum: [created, title]
        default: created

  responses:
    '200':
      description: OK
      content:
        application/json:
          schema:
            type: object
            properties:
              meta:
                $ref: '../../openapi.yaml#/components/schemas/MetaListResponse'
              data:
                type: array
                items:
                  type: object
                  properties:
                    id:
                      type: string
                      format: uuid
                      description: Resource ID
                      example: '123e4567-e89b-12d3-a456-426614174000'
                    title:
                      type: string
                      description: Resource title
                      example: 'Example Title'
                    createdAtDatetime:
                      type: string
                      format: date-time
                      description: Creation timestamp
                      example: '2024-11-10T23:00:00.000Z'
    '400':
      $ref: '../../openapi.yaml#/components/schemas/Error400'
    '401':
      $ref: '../../openapi.yaml#/components/schemas/Error401'
    '403':
      $ref: '../../openapi.yaml#/components/schemas/Error403'
```

### Template: Get Single Item (GET by ID)

```yaml
get:
  operationId: get-{resource}-by-id
  summary: Get {resource} details
  description: |
    <small>**Target Roles:**</small> `{role}`

    Returns detailed information about a specific {resource}.

  tags:
    - {Tag}

  parameters:
    - $ref: '../../../openapi.yaml#/components/parameters/Authorization'
    - $ref: '../../../openapi.yaml#/components/parameters/{ResourceId}'

  responses:
    '200':
      description: OK
      headers:
        Last-Modified:
          $ref: '../../../openapi.yaml#/components/headers/LastModified'
      content:
        application/json:
          schema:
            type: object
            properties:
              meta:
                $ref: '../../../openapi.yaml#/components/schemas/MetaItemResponse'
              data:
                type: object
                properties:
                  id:
                    type: string
                    format: uuid
                    example: '123e4567-e89b-12d3-a456-426614174000'
                  # ... other properties
    '400':
      $ref: '../../../openapi.yaml#/components/schemas/Error400'
    '401':
      $ref: '../../../openapi.yaml#/components/schemas/Error401'
    '403':
      $ref: '../../../openapi.yaml#/components/schemas/Error403'
    '404':
      $ref: '../../../openapi.yaml#/components/schemas/Error404'
```

### Template: Create Resource (POST)

```yaml
post:
  operationId: create-{resource}
  summary: Create {resource}
  description: |
    <small>**Target Roles:**</small> `{role}`

    Creates a new {resource}.

  tags:
    - {Tag}

  parameters:
    - $ref: '../../openapi.yaml#/components/parameters/ContentTypeJson'
    - $ref: '../../openapi.yaml#/components/parameters/Authorization'

  requestBody:
    required: true
    content:
      application/json:
        schema:
          type: object
          description: Request body for creating {resource}
          required: [field1, field2]
          properties:
            field1:
              type: string
              description: Field description
              example: 'example value'
            field2:
              type: string
              description: Field description
              example: 'example value'
            optionalField:
              type: string
              nullable: true
              description: Optional field
              example: null

  responses:
    '201':
      description: Created
      x-summary: <strong>Created</strong> - {Resource} created successfully
      content:
        application/json:
          schema:
            type: object
            properties:
              meta:
                $ref: '../../openapi.yaml#/components/schemas/MetaCreated'
              data:
                type: object
                properties:
                  id:
                    type: string
                    format: uuid
                    example: '123e4567-e89b-12d3-a456-426614174000'
                  # ... other properties
          examples:
            success:
              summary: Successful creation
              value:
                meta: { status: 201 }
                data:
                  id: '123e4567-e89b-12d3-a456-426614174000'
                  # ... example data
    '400':
      $ref: '../../openapi.yaml#/components/schemas/Error400'
    '401':
      $ref: '../../openapi.yaml#/components/schemas/Error401'
    '403':
      $ref: '../../openapi.yaml#/components/schemas/Error403'
```

### Template: Update Resource (PATCH)

```yaml
patch:
  operationId: update-{resource}
  summary: Update {resource}
  description: |
    <small>**Target Roles:**</small> `{role}`

    Updates an existing {resource}. Only provided fields are updated.

  tags:
    - {Tag}

  parameters:
    - $ref: '../../../openapi.yaml#/components/parameters/ContentTypeJson'
    - $ref: '../../../openapi.yaml#/components/parameters/Authorization'
    - $ref: '../../../openapi.yaml#/components/parameters/{ResourceId}'

  requestBody:
    required: true
    content:
      application/json:
        schema:
          type: object
          properties:
            field1:
              type: string
              description: Field to update
              example: 'new value'

  responses:
    '200':
      description: OK
      x-summary: <strong>OK</strong> - {Resource} updated successfully
      content:
        application/json:
          schema:
            type: object
            properties:
              meta:
                $ref: '../../../openapi.yaml#/components/schemas/MetaItemResponse'
              data:
                type: object
                properties:
                  id:
                    type: string
                    format: uuid
                    example: '123e4567-e89b-12d3-a456-426614174000'
                  # ... updated properties
    '400':
      $ref: '../../../openapi.yaml#/components/schemas/Error400'
    '401':
      $ref: '../../../openapi.yaml#/components/schemas/Error401'
    '403':
      $ref: '../../../openapi.yaml#/components/schemas/Error403'
    '404':
      $ref: '../../../openapi.yaml#/components/schemas/Error404'
```

### Template: Delete Resource (DELETE)

```yaml
delete:
  operationId: delete-{resource}
  summary: Delete {resource}
  description: |
    <small>**Target Roles:**</small> `{role}`

    Permanently deletes the {resource}.

  tags:
    - {Tag}

  parameters:
    - $ref: '../../../openapi.yaml#/components/parameters/Authorization'
    - $ref: '../../../openapi.yaml#/components/parameters/{ResourceId}'

  responses:
    '204':
      description: No Content
      x-summary: <strong>No Content</strong> - {Resource} deleted successfully
    '401':
      $ref: '../../../openapi.yaml#/components/schemas/Error401'
    '403':
      $ref: '../../../openapi.yaml#/components/schemas/Error403'
    '404':
      $ref: '../../../openapi.yaml#/components/schemas/Error404'
```

## Field Reference Guide

### operationId

**Format:** `{verb}-{resource}-{action}`

**Rules:**
- Use kebab-case (lowercase with dashes)
- Must be unique across entire API
- Be descriptive and consistent

**Examples:**
- `get-scenario-list`
- `get-scenario-by-id`
- `create-scenario`
- `update-scenario`
- `delete-scenario`
- `post-auth-login`

### summary

**Format:** `{Action} {resource} {status-emoji}`

**Keep it short (2-5 words):**
- `Login`
- `List of scenarios`
- `Create scenario 🟡`
- `Update conversation 🟠`

**Status emojis:**
- 🟡 = TODO
- 🟠 = WIP
- 🔴 = ERROR
- 🔘 = DESCOPED
- ⚠️ = Warning

### description

**Format:** Markdown with Target Roles

```yaml
description: |
  <small>**Target Roles:**</small> `role1`, `role2`

  Main description paragraph explaining endpoint purpose.

  Optional additional details or business logic notes.
```

**Target Roles notation:**
- `anonymous` → No auth required
- `admin`, `customer` → Specific role
- `>` → "at least" (e.g., `customer > contributor`)

### tags

**Use existing tags:**
- `Authentication`
- `Scenarios`
- `Conversations`
- `Users`

Only create new tags if necessary (add to main `openapi.yaml` first).

### security

```yaml
# Option 1: Require authentication (default, omit field)
# (no security field)

# Option 2: Anonymous access (explicit override)
security: []
```

**Use `security: []` for:**
- Login endpoint
- Password reset request
- Public endpoints

### parameters

**Reusable parameters (preferred):**

```yaml
parameters:
  - $ref: '../../../openapi.yaml#/components/parameters/Authorization'
  - $ref: '../../../openapi.yaml#/components/parameters/ScenarioId'
```

**Common parameters:**
- `Authorization` → Bearer token
- `ContentTypeJson` → JSON content type
- `PageSize`, `PageNumber` → Pagination
- `ScenarioId`, `ConversationId`, `UserId` → Path params

**Inline parameters (endpoint-specific):**

```yaml
parameters:
  - name: filter[status]
    in: query
    required: false
    description: Filter by status
    schema:
      type: string
      enum: [active, archived]
```

**Relative path depth:**

| Depth | Path Example | Relative Path |
|-------|--------------|---------------|
| 2 | `v1/auth/` | `../../openapi.yaml` |
| 3 | `v1/auth/login/` | `../../../openapi.yaml` |
| 4 | `v1/scenarios/{scenarioId}/` | `../../../../openapi.yaml` |

### requestBody

**Only for:** POST, PATCH, PUT

```yaml
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [mandatoryField]
        properties:
          mandatoryField:
            type: string
            description: Description
            example: 'example'
          optionalField:
            type: string
            nullable: true
            description: Optional field
            example: null
```

**Property attributes:**

| Attribute | Required | Description |
|-----------|----------|-------------|
| `type` | Yes | `string`, `number`, `boolean`, `array`, `object` |
| `description` | Yes | Human-readable description |
| `example` | Yes | Realistic example value |
| `format` | No | `uuid`, `email`, `date`, `date-time`, `uri` |
| `enum` | No | List of allowed values |
| `nullable` | No | Set to `true` if field can be `null` |

**Nested objects:**

```yaml
properties:
  user:
    type: object
    properties:
      email:
        type: string
        format: email
        description: User email
        example: 'user@example.com'
```

**Arrays:**

```yaml
properties:
  tags:
    type: array
    description: List of tags
    items:
      type: string
      example: 'tag1'
    example: ['tag1', 'tag2']
```

### responses

**Standard status codes:**
- `200` OK → GET success
- `201` Created → POST success
- `204` No Content → DELETE success
- `400` Bad Request → Validation error
- `401` Unauthorized → Missing/invalid auth
- `403` Forbidden → Not authorized
- `404` Not Found → Resource doesn't exist

**Response structure:**

```yaml
responses:
  '200':
    description: OK
    x-summary: <strong>OK</strong> - Extended description
    content:
      application/json:
        schema:
          type: object
          properties:
            meta:
              $ref: '../../../openapi.yaml#/components/schemas/MetaItemResponse'
            data:
              type: object
              properties:
                # ... data structure
        examples:
          success:
            summary: Successful response
            value:
              meta: { status: 200 }
              data: { id: '123e4567-e89b-12d3-a456-426614174000' }
  '400':
    $ref: '../../../openapi.yaml#/components/schemas/Error400'
  '401':
    $ref: '../../../openapi.yaml#/components/schemas/Error401'
```

**Meta schemas:**
- `MetaItemResponse` → Single item
- `MetaListResponse` → List with pagination
- `MetaCreated` → Creation response

**Always include error responses:**
```yaml
'400': $ref: '../../../openapi.yaml#/components/schemas/Error400'
'401': $ref: '../../../openapi.yaml#/components/schemas/Error401'
'403': $ref: '../../../openapi.yaml#/components/schemas/Error403'
'404': $ref: '../../../openapi.yaml#/components/schemas/Error404'  # if applicable
```

## Creating Reusable Components

### When to Create a Component

**Create when:**
- Used in 2+ endpoints
- Represents standard pattern
- Complex definition

**Keep inline when:**
- Endpoint-specific
- Simple (1-2 properties)
- Unique business rule

### Component Types

#### Parameters (`components/parameters/`)

**Template:**
```yaml
name: {parameterName}
in: {path|query|header}
required: {true|false}
description: Description
schema:
  type: {type}
  format: {format}  # Optional
  example: 'example'
```

**Example: `scenario-id.yaml`**
```yaml
name: scenarioId
in: path
required: true
description: UUID of the scenario
schema:
  type: string
  format: uuid
  example: '9a4cd70f-6437-497a-955d-9a840d5d8b6b'
```

**Register in openapi.yaml:**
```yaml
components:
  parameters:
    ScenarioId:
      $ref: './components/parameters/scenario-id.yaml'
```

#### Schemas (`components/schemas/`)

**Template:**
```yaml
type: object
description: Description
properties:
  field:
    type: {type}
    description: Description
    example: 'example'
required: [field1, field2]
```

**Example: `scenario-response.yaml`**
```yaml
type: object
description: Scenario information
properties:
  id:
    type: string
    format: uuid
    description: Scenario ID
    example: '9a4cd70f-6437-497a-955d-9a840d5d8b6b'
  title:
    type: string
    description: Scenario title
    example: 'Parent-Child Conversation'
  createdAtDatetime:
    type: string
    format: date-time
    description: Creation timestamp
    example: '2024-11-10T23:00:00.000Z'
required: [id, title, createdAtDatetime]
```

**Register in openapi.yaml:**
```yaml
components:
  schemas:
    ScenarioResponse:
      $ref: './components/schemas/scenario-response.yaml'
```

## Data Modeling Best Practices

### Property Naming

**Good:**
```yaml
properties:
  firstName:
    type: string
  createdAtDatetime:
    type: string
    format: date-time
  accessTokenExpiresInDuration:
    type: number
```

**Bad:**
```yaml
properties:
  name:      # Too vague
  created:   # Missing type suffix
  expires:   # Missing type suffix
```

### Date/Time Conventions

- `{name}Datetime` → ISO 8601: `2020-10-19T13:28:06.419Z`
- `{name}Date` → Date: `1980-05-21`
- `{name}Duration` → Milliseconds (number)
- `{name}Time` → Milliseconds from midnight (number)

### Data Types

| Data | Type | Format | Example |
|------|------|--------|---------|
| UUID | `string` | `uuid` | `'9a4cd70f-6437-497a-955d-9a840d5d8b6b'` |
| Email | `string` | `email` | `'user@example.com'` |
| URL | `string` | `uri` | `'https://example.com'` |
| Datetime | `string` | `date-time` | `'2024-11-10T23:00:00.000Z'` |
| Date | `string` | `date` | `'2024-11-10'` |
| Integer | `integer` | - | `42` |
| Number | `number` | - | `42.5` |
| Boolean | `boolean` | - | `true` |

### Always Include Examples

Every property needs a realistic example:

```yaml
email:
  type: string
  format: email
  description: User email
  example: 'john.doe@example.com'  # ✅ Good
```

### Handle Nullable Values

```yaml
middleName:
  type: string
  nullable: true
  description: Middle name (null if not provided)
  example: null
```

## Documentation Checklist

### ✅ File Structure
- [ ] File in correct directory matching URL path
- [ ] File named correctly (`{METHOD}.yaml` or combined)
- [ ] Directory structure created if new path

### ✅ Registration
- [ ] Path added to `openapi.yaml` `paths:` section
- [ ] `$ref` points to correct file

### ✅ Required Fields
- [ ] `operationId` is unique and follows convention
- [ ] `summary` is clear and concise
- [ ] `description` includes Target Roles
- [ ] `tags` includes appropriate tag
- [ ] `parameters` includes all required params
- [ ] `responses` includes success and error codes

### ✅ Data Quality
- [ ] All properties have `type`
- [ ] All properties have `description`
- [ ] All properties have `example`
- [ ] Required fields marked in `required` array
- [ ] Nullable fields marked with `nullable: true`

### ✅ Consistency
- [ ] Naming follows conventions (camelCase)
- [ ] Date/time properties have correct suffixes
- [ ] UUIDs use `format: uuid`
- [ ] Examples are realistic

### ✅ Reusability
- [ ] Common parameters referenced via `$ref`
- [ ] Error responses use standard schemas
- [ ] New components created if reusable

### ✅ Validation
- [ ] YAML syntax is valid
- [ ] All `$ref` links are correct
- [ ] Relative paths are accurate
- [ ] Documentation previews correctly in ReDoc

## Validation

### Preview in ReDoc

1. Open `documentation/rest-api/openapi.yaml`
2. Press `F1` → "OpenAPI: show preview using ReDoc"
3. Verify endpoint appears and renders correctly
4. Check examples display properly

### Check References

- Click through `$ref` links in editor
- Verify all components resolve
- Test relative paths

## Common Mistakes to Avoid

### ❌ Incorrect Relative Paths

```yaml
# Wrong - count the depth correctly
$ref: '../openapi.yaml#/components/parameters/ScenarioId'

# Right
$ref: '../../../../openapi.yaml#/components/parameters/ScenarioId'
```

### ❌ Missing Required Fields

```yaml
# Wrong
email:
  type: string

# Right
email:
  type: string
  description: User email
  example: 'user@example.com'
```

### ❌ Inconsistent Naming

```yaml
# Wrong
created: '2024-11-10T23:00:00.000Z'

# Right
createdAtDatetime: '2024-11-10T23:00:00.000Z'
```

### ❌ Forgetting to Register

Always update `openapi.yaml` `paths:` section after creating endpoint file.

### ❌ Incomplete Error Responses

```yaml
# Wrong - missing error codes
responses:
  '200': ...

# Right - include all relevant errors
responses:
  '200': ...
  '400': $ref: ...
  '401': $ref: ...
  '403': $ref: ...
  '404': $ref: ...
```

## Quick Reference

### File Naming
- Endpoints: `{METHOD}.yaml` (uppercase)
- Components: `{name-with-dashes}.yaml` (lowercase)

### Operation ID Format
`{verb}-{resource}-{action}` (e.g., `get-scenario-list`)

### Target Roles
```
<small>**Target Roles:**</small> `role1`, `role2`
```

### Status Symbols
🟡 TODO | 🟠 WIP | 🔴 ERROR | 🔘 DESCOPED | ⚠️ Warning

### Standard Responses
- 200 OK, 201 Created, 204 No Content
- 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found

### Meta Schemas
- `MetaItemResponse` → Single item
- `MetaListResponse` → List with pagination
- `MetaCreated` → Creation response
