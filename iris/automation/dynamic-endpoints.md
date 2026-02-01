---
layout: default
title: Dynamic endpoints
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 30
has_children: true
has_toc: false
permalink: /iris/automation/dynamic-endpoints/
---

# Dynamic endpoints
{: .no_toc }

A dynamic endpoint is a custom API route that executes a Jyro script when called. The script receives the HTTP request data and controls the response. They can be used to build integrations, custom reports, webhook receivers, custom data output and more.

**Example**: Create a custom report endpoint

```
GET /api/dynamic/v1/org-summary?orgId=abc-123
```

Returns:
```json
{
  "name": "Acme Corporation",
  "childCount": 5,
  "isActive": true
}
```



## Request Data

Your script receives HTTP request information via `Data.request`:

```jyro
Data.request = {
  method: "POST",
  path: "/api/dynamic/v1/my-endpoint",
  queryString: "?param1=value1",
  query: {
    param1: "value1",
    param2: "value2"
  },
  headers: {
    "authorization": "Bearer eyJ...",
    "content-type": "application/json"
  },
  body: { ... },  # For POST/PUT/PATCH
  user: {
    isAuthenticated: true,
    name: "john@example.com",
    principalId: "user:john@example.com",
    roles: ["user", "admin"]
  }
}
```

### Query Parameters

```jyro
# Access query parameters
var orgId = Data.request.query.orgId
var limit = Data.request.query.limit

# Check if parameter exists
if Data.request.query.filter != null then
  var filter = Data.request.query.filter
end
```

### Request Body

For POST, PUT, and PATCH requests:

```jyro
# Access body properties
var name = Data.request.body.name
var email = Data.request.body.email
var items = Data.request.body.items
```

### Headers

Headers are lowercase:

```jyro
var authHeader = Data.request.headers.authorization
var customHeader = Data.request.headers["x-custom-header"]
```

### User Information

```jyro
# Check authentication
if Data.request.user.isAuthenticated == false then
  Data._payload = { error: "Authentication required" }
  Data._statusCode = 401
  return
end

# Get user details
var userName = Data.request.user.name
var userId = Data.request.user.principalId

# Check roles
var isAdmin = false
foreach role in Data.request.user.roles do
  if role == "admin" then
    isAdmin = true
  end
end
```

## Response Control

Control the HTTP response using special properties:

```jyro
# Set response body (JSON serialized)
Data._payload = {
  success: true,
  data: { ... }
}

# Set HTTP status code
Data._statusCode = 200

# Optional: Redirect
Data._redirect = "/success-page"
```

### Status Codes

| Code | Meaning | When to Use |
|------|---------|-------------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST that creates data |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid input data |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | User lacks permission |
| 404 | Not Found | Resource doesn't exist |
| 500 | Server Error | Unexpected error |

## Authentication & Authorization

### Requiring Authentication

Set **Requires Authentication** to `true` to require a valid JWT token.

Unauthenticated requests receive:
```json
{
  "error": "Authentication required"
}
```
Status: 401 Unauthorized

### Role-Based Access

Set **Allowed Roles** to restrict access to specific roles:

- Leave empty: Any authenticated user can access
- Add roles: Only users with those roles can access

Example: `["admin", "manager"]` - Only admins and managers can call this endpoint

### Public Endpoints

For webhook receivers or public APIs:
- Set **Requires Authentication** to `false`
- Leave **Allowed Roles** empty

> ⚠ Public endpoints can be called by anyone. Validate webhook signatures or use other security measures.

## Common Use Cases

### Custom Report Endpoint

```jyro
# GET /api/dynamic/v1/organization-report?orgId=xxx

# Validate parameters
if Data.request.query.orgId == null then
  Data._payload = { error: "orgId parameter is required" }
  Data._statusCode = 400
  return
end

# Get organization
var org = GetOrganizationById(Data.request.query.orgId)

if org == null then
  Data._payload = { error: "Organization not found" }
  Data._statusCode = 404
  return
end

# Get related data
var children = GetChildOrganizations(org.id)
var facets = GetOrganizationFacetInstances(org.id)

# Build response
Data._payload = {
  organization: {
    id: org.id,
    name: org.name,
    description: org.description,
    isActive: org.isActive
  },
  childCount: Length(children),
  facetCount: Length(facets),
  generatedAt: Now()
}
Data._statusCode = 200
```

### Create Resource Endpoint

```jyro
# POST /api/dynamic/v1/quick-org

# Validate request body
if Data.request.body.name == null then
  Data._payload = { error: "name is required" }
  Data._statusCode = 400
  return
end

# Create organization
var newOrg = CreateOrganization(
  Data.request.body.name,
  Data.request.body.description or "",
  Data.request.body.parentId
)

if newOrg != null then
  Log("Information", "Created organization via API: " + newOrg.name)

  Data._payload = {
    success: true,
    organizationId: newOrg.id,
    name: newOrg.name
  }
  Data._statusCode = 201
else
  Data._payload = { error: "Failed to create organization" }
  Data._statusCode = 500
end
```

### Webhook Receiver

```jyro
# POST /api/dynamic/v1/webhooks/external-system
# Requires Authentication: false

# Validate webhook signature
var signature = Data.request.headers["x-webhook-signature"]
var expectedSecret = GetSecret("webhook-secret")

if signature == null then
  Log("Warning", "Webhook received without signature")
  Data._payload = { error: "Missing signature" }
  Data._statusCode = 401
  return
end

# Process webhook payload
var event = Data.request.body

Log("Information", "Webhook received: " + event.type)

if event.type == "user.created" then
  # Handle user creation
  BroadcastNotification(
    "External User Created",
    "User " + event.data.email + " created in external system",
    "Information",
    null,
    true
  )
elif event.type == "user.deleted" then
  # Handle user deletion
  Log("Warning", "User deleted in external system: " + event.data.email)
end

Data._payload = { received: true }
Data._statusCode = 200
```

### Bulk Operation Endpoint

```jyro
# POST /api/dynamic/v1/bulk-update

# Validate input
if Data.request.body.organizationIds == null then
  Data._payload = { error: "organizationIds array is required" }
  Data._statusCode = 400
  return
end

var orgIds = Data.request.body.organizationIds
var facetId = Data.request.body.facetDefinitionId
var values = Data.request.body.values

var results = {
  total: Length(orgIds),
  succeeded: 0,
  failed: 0,
  failures: []
}

foreach orgId in orgIds do
  var org = GetOrganizationById(orgId)

  if org == null then
    results.failed = results.failed + 1
    results.failures.add({
      id: orgId,
      error: "Not found"
    })
  else
    SetOrganizationFacetInstance(orgId, facetId, values)
    results.succeeded = results.succeeded + 1
  end
end

Log("Information", "Bulk update: " + results.succeeded + " succeeded, " + results.failed + " failed")

Data._payload = results
Data._statusCode = 200
```

### Search Endpoint

```jyro
# GET /api/dynamic/v1/search?q=acme&limit=10

var query = Data.request.query.q or ""
var limit = Data.request.query.limit or 20

if query == "" then
  Data._payload = { error: "q parameter is required" }
  Data._statusCode = 400
  return
end

var allOrgs = GetAllOrganizations()
var matches = []

foreach org in allOrgs do
  # Simple contains search (case-insensitive would need custom logic)
  if org.name.contains(query) then
    matches.add({
      id: org.id,
      name: org.name,
      isActive: org.isActive
    })

    if Length(matches) >= limit then
      break
    end
  end
end

Data._payload = {
  query: query,
  count: Length(matches),
  results: matches
}
Data._statusCode = 200
```

## Endpoint Categories

Use categories to organize your endpoints:

- `OrganizationReport` - Organization-related reports
- `PersonReport` - Person-related reports
- `Integration` - External system integrations
- `Webhook` - Webhook receivers
- `Utility` - Utility endpoints

Categories appear in the Dynamic Endpoints list for filtering.

## Testing Endpoints

### Using the Scripting Dashboard

1. Navigate to your dynamic endpoint
2. Click **Test**
3. Configure test parameters:
   - Query parameters
   - Request body (for POST/PUT/PATCH)
   - Headers
4. Click **Execute**
5. View the response

### Using External Tools

Test with curl or Postman:

```bash
# GET request
curl -H "Authorization: Bearer YOUR_TOKEN" \
  "https://your-tenant.iriscloud.app/api/dynamic/v1/org-summary?orgId=abc-123"

# POST request
curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "Test Org", "description": "Created via API"}' \
  "https://your-tenant.iriscloud.app/api/dynamic/v1/quick-org"
```

## Best Practices

### 1. Validate All Input

```jyro
# Always validate required parameters
if Data.request.query.id == null then
  Data._payload = { error: "id is required" }
  Data._statusCode = 400
  return
end
```

### 2. Return Appropriate Status Codes

```jyro
# 404 for not found
if org == null then
  Data._payload = { error: "Not found" }
  Data._statusCode = 404
  return
end

# 400 for bad input
if name == "" then
  Data._payload = { error: "Name cannot be empty" }
  Data._statusCode = 400
  return
end
```

### 3. Log Important Operations

```jyro
Log("Information", "API called: " + Data.request.path)
Log("Information", "User: " + Data.request.user.name)

# ... processing ...

Log("Information", "API completed successfully")
```

### 4. Use Consistent Response Format

```jyro
# Success
Data._payload = {
  success: true,
  data: { ... }
}

# Error
Data._payload = {
  success: false,
  error: "Description of what went wrong"
}
```

### 5. Handle Errors Gracefully

```jyro
var org = GetOrganizationById(orgId)

if org == null then
  Log("Warning", "Organization not found: " + orgId)
  Data._payload = { error: "Organization not found" }
  Data._statusCode = 404
  return
end
```

## In this section

- [Create a dynamic endpoint](/iris/automation/dynamic-endpoints/create-dynamic-endpoint/): Step-by-step guide to creating a dynamic endpoint

## Next steps

- [Data context](/iris/automation/data-context/): Understand request data structure
- [Event queue](/iris/automation/event-queue/): Monitor endpoint executions
- [Templates](/iris/automation/templates/): Generate formatted responses
