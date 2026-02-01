---
layout: default
title: Data context
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 60
permalink: /iris/automation/data-context/
---

# Data Context

Every script has access to contextual information through the `Data` object. The structure of this object varies based on the script type and the event that triggered it.

## Event Hook Data Context

When an event hook runs, the `Data` object contains information about the entity being created, updated, or deleted.

### Create Events

For `BeforeOrganizationCreated` and `AfterOrganizationCreated`:

```jyro
Data.organization = {
  id: "guid-here",
  tenantId: "tenant-id",
  name: "Acme Corporation",
  description: "A sample organization",
  isActive: true,
  parentOrganizationId: null,
  createdAt: "2024-01-15T10:30:00Z",
  updatedAt: "2024-01-15T10:30:00Z",
  facetInstances: [...],
  fieldInstances: [...],
  entitlementInstances: [...],
  hybridListInstanceItems: [...]
}
```

For `BeforePersonCreated` and `AfterPersonCreated`:

```jyro
Data.person = {
  id: "guid-here",
  tenantId: "tenant-id",
  firstName: "John",
  lastName: "Smith",
  emailAddress: "john.smith@example.com",
  isActive: true,
  createdAt: "2024-01-15T10:30:00Z",
  updatedAt: "2024-01-15T10:30:00Z",
  facetInstances: [...],
  fieldInstances: [...],
  entitlementInstances: [...]
}
```

### Update Events

For update events, you have access to both the current and previous state:

```jyro
BeforeOrganizationUpdated:
  # Current state (being saved)
  Log("Information", "Current name: " + Data.current.name)

  # Previous state (before changes)
  if Data.previous != null then
    Log("Information", "Previous name: " + Data.previous.name)
  end

AfterPersonUpdated:
  # Compare values to detect changes
  if Data.previous != null then
    if Data.current.emailAddress != Data.previous.emailAddress then
      Log("Information", "Email changed from " + Data.previous.emailAddress + " to " + Data.current.emailAddress)
    end
  end
```

### Delete Events

Delete events provide the entity being deleted:

```jyro
BeforeOrganizationDeleted:
  Log("Warning", "About to delete: " + Data.organization.name)

AfterPersonDeleted:
  Log("Information", "Person deleted: " + Data.person.firstName + " " + Data.person.lastName)
```

## Entity Properties

### Organization Properties

| Property | Type | Description |
|----------|------|-------------|
| `id` | string | Unique identifier (GUID) |
| `tenantId` | string | Tenant identifier |
| `name` | string | Organization name |
| `description` | string | Organization description |
| `isActive` | boolean | Active status |
| `parentOrganizationId` | string | Parent organization ID (null if root) |
| `createdAt` | string | Creation timestamp (ISO 8601) |
| `updatedAt` | string | Last update timestamp |
| `facetInstances` | array | Facet values attached to this organization |
| `fieldInstances` | array | Field values attached to this organization |
| `entitlementInstances` | array | Entitlements attached to this organization |
| `hybridListInstanceItems` | array | Hybrid list items |

### Person Properties

| Property | Type | Description |
|----------|------|-------------|
| `id` | string | Unique identifier (GUID) |
| `tenantId` | string | Tenant identifier |
| `firstName` | string | First name |
| `lastName` | string | Last name |
| `emailAddress` | string | Email address |
| `isActive` | boolean | Active status |
| `createdAt` | string | Creation timestamp (ISO 8601) |
| `updatedAt` | string | Last update timestamp |
| `facetInstances` | array | Facet values attached to this person |
| `fieldInstances` | array | Field values attached to this person |
| `entitlementInstances` | array | Entitlements attached to this person |

### Facet Instance Structure

```jyro
facetInstances = [
  {
    facetDefinitionId: "guid-here",
    values: ["Value 1", "Value 2"]
  },
  {
    facetDefinitionId: "another-guid",
    values: ["Single Value"]
  }
]
```

### Field Instance Structure

```jyro
fieldInstances = [
  {
    fieldDefinitionId: "guid-here",
    items: [
      {
        key: "item-key",
        values: ["Value 1", "Value 2"]
      }
    ]
  }
]
```

## Dynamic Endpoint Data Context

Dynamic endpoints receive HTTP request information through `Data.request`:

```jyro
Data.request = {
  method: "GET",
  path: "/api/dynamic/v1/my-endpoint",
  queryString: "?param1=value1&param2=value2",
  query: {
    param1: "value1",
    param2: "value2"
  },
  headers: {
    "authorization": "Bearer eyJ...",
    "content-type": "application/json",
    "x-custom-header": "custom-value"
  },
  body: { ... },  # For POST/PUT/PATCH requests
  user: {
    isAuthenticated: true,
    name: "john@example.com",
    principalId: "user:john@example.com",
    roles: ["user", "admin"]
  }
}
```

### Accessing Query Parameters

```jyro
# Single parameter
var orgId = Data.request.query.orgId

# Check if parameter exists
if Data.request.query.limit != null then
  var limit = Data.request.query.limit
else
  var limit = 10
end
```

### Accessing Request Body

For POST, PUT, and PATCH requests:

```jyro
# Access body properties
var name = Data.request.body.name
var email = Data.request.body.email
var items = Data.request.body.items

# Validate required fields
if Data.request.body.name == null then
  Data._payload = { error: "name is required" }
  Data._statusCode = 400
  return
end
```

### Accessing Headers

Headers are lowercase for consistency:

```jyro
var authHeader = Data.request.headers.authorization
var contentType = Data.request.headers["content-type"]
var customHeader = Data.request.headers["x-custom-header"]
```

### Checking Authentication

```jyro
if Data.request.user.isAuthenticated == false then
  Data._payload = { error: "Authentication required" }
  Data._statusCode = 401
  return
end

# Check user roles
var isAdmin = false
foreach role in Data.request.user.roles do
  if role == "admin" then
    isAdmin = true
  end
end
```

## Scheduled Script Data Context

Scheduled scripts receive input data through `Data.input` if configured:

```jyro
# If the scheduled script has InputDataJson configured:
var batchSize = Data.input.batchSize
var targetOrg = Data.input.targetOrganizationId
```

If no input data is configured, `Data.input` will be null.

## Getting Current Context

All scripts can access the current execution context:

```jyro
# Get current user information
var user = GetCurrentUser()
# Returns: { principalId: "user:email@example.com", name: "email@example.com" }

# Get current tenant
var tenant = GetCurrentTenant()
# Returns: { tenantId: "tenant-id" }
```

## Null-Safe Property Access

Always check for null values before accessing nested properties:

```jyro
# Check before accessing
if Data.organization != null then
  var name = Data.organization.name
end

# Check nested properties
if Data.organization.parentOrganizationId != null then
  var parent = GetOrganizationById(Data.organization.parentOrganizationId)
end

# Default value pattern
var description = Data.organization.description
if description == null then
  description = "No description provided"
end
```

## Working with Collections

### Iterating Facet Instances

```jyro
var facets = Data.organization.facetInstances

if facets != null and Length(facets) > 0 then
  foreach facet in facets do
    Log("Debug", "Facet ID: " + facet.facetDefinitionId)

    foreach value in facet.values do
      Log("Debug", "  Value: " + value)
    end
  end
end
```

### Finding a Specific Facet

```jyro
var targetFacetId = GetFacetDefinitionByName("Primary Domain")
var foundValue = null

foreach facet in Data.organization.facetInstances do
  if facet.facetDefinitionId == targetFacetId then
    if Length(facet.values) > 0 then
      foundValue = facet.values[0]
    end
  end
end

if foundValue != null then
  Log("Information", "Primary Domain: " + foundValue)
end
```

## Setting Response Data (Dynamic Endpoints)

Control the HTTP response using special properties:

```jyro
# Set response body (JSON serialized)
Data._payload = {
  success: true,
  data: {
    id: org.id,
    name: org.name
  }
}

# Set HTTP status code
Data._statusCode = 200

# Optional: Redirect to another URL
Data._redirect = "/success"
```

Common status codes:

| Code | Meaning | Use When |
|------|---------|----------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST that creates a resource |
| 400 | Bad Request | Invalid input data |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | User lacks permission |
| 404 | Not Found | Resource doesn't exist |
| 500 | Server Error | Unexpected error |

## Next Steps

- [Event Hooks](event-hooks) - Use data context in event hooks
- [Dynamic Endpoints](dynamic-endpoints) - Use request data in custom APIs
- [Log Lines](log-lines) - Log data context for debugging
