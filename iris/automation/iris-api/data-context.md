---
layout: default
title: Data context reference
parent: Iris API
grand_parent: Automation
nav_order: 20
permalink: /iris/automation/iris-api/data-context/
---

# Data context reference
{: .no_toc }

This document describes the structure of the `Data` object passed to Jyro scripts in Event Hooks and Dynamic Endpoints. Understanding this structure is essential for writing effective scripts.

## Event Hooks

Event hooks receive contextual data about the domain operation being performed. The structure of the `Data` object varies based on the event type.

### Create and Delete Events

For **create** and **delete** events, the entity is available directly under a property named after the entity type:

| Event Type | Data Property | Description |
|------------|---------------|-------------|
| `BeforeOrganizationCreated` | `Data.organization` | The organization being created |
| `AfterOrganizationCreated` | `Data.organization` | The organization that was created |
| `BeforeOrganizationDeleted` | `Data.organization` | The organization being deleted |
| `AfterOrganizationDeleted` | `Data.organization` | The organization that was deleted |
| `BeforePersonCreated` | `Data.person` | The person being created |
| `AfterPersonCreated` | `Data.person` | The person that was created |
| `BeforePersonDeleted` | `Data.person` | The person being deleted |
| `AfterPersonDeleted` | `Data.person` | The person that was deleted |

**Example - Create Event:**

```jyro
# BeforeOrganizationCreated or AfterOrganizationCreated
Log("Information", "Organization Name: " + Data.organization.Name)
Log("Information", "Organization ID: " + Data.organization.Id)
Log("Information", "Is Active: " + Data.organization.IsActive)
```

**Example - Delete Event:**

```jyro
# BeforePersonDeleted
Log("Warning", "Deleting person: " + Data.person.FirstName + " " + Data.person.LastName)
Log("Warning", "Email: " + Data.person.EmailAddress)
```

### Update Events

For **update** events, both the current (new) state and previous (old) state are provided:

| Event Type | Data Properties | Description |
|------------|-----------------|-------------|
| `BeforeOrganizationUpdated` | `Data.current`, `Data.previous` | Current = new values, Previous = old values |
| `AfterOrganizationUpdated` | `Data.current`, `Data.previous` | Current = saved values, Previous = old values |
| `BeforePersonUpdated` | `Data.current`, `Data.previous` | Current = new values, Previous = old values |
| `AfterPersonUpdated` | `Data.current`, `Data.previous` | Current = saved values, Previous = old values |

**Important:** For async events (e.g., `AsyncOrganizationUpdated`), `Data.previous` may be `null` as the previous state is not retained for performance reasons.

**Example - Update Event:**

```jyro
# BeforeOrganizationUpdated or AfterOrganizationUpdated
Log("Information", "Organization updated: " + Data.current.Name)

# Compare old vs new values
if Data.previous != null then
    if Data.current.Name != Data.previous.Name then
        Log("Information", "Name changed from '" + Data.previous.Name + "' to '" + Data.current.Name + "'")
    end

    if Data.current.IsActive != Data.previous.IsActive then
        Log("Warning", "IsActive changed from " + Data.previous.IsActive + " to " + Data.current.IsActive)
    end
end
```

### Entity Property Reference

#### Organization Properties

```json
{
  "Id": "guid-string",
  "TenantId": "string",
  "Name": "string",
  "Description": "string or null",
  "IsActive": true,
  "ParentOrganizationId": "guid-string or null",
  "FacetInstances": [
    {
      "FacetDefinitionId": "guid-string",
      "Values": ["value1", "value2"]
    }
  ],
  "FieldInstances": [
    {
      "FieldDefinitionId": "guid-string",
      "Items": [
        {
          "Value": "string",
          "IsPrimary": true
        }
      ]
    }
  ],
  "HybridListInstanceItems": [
    {
      "HybridListDefinitionId": "guid-string",
      "CategoryId": "guid-string",
      "ItemId": "guid-string or null",
      "FreeTextValue": "string or null"
    }
  ],
  "EntitlementInstances": [],
  "CreatedAt": "2024-01-15T10:30:00Z",
  "UpdatedAt": "2024-01-15T10:30:00Z"
}
```

#### Person Properties

```json
{
  "Id": "guid-string",
  "TenantId": "string",
  "FirstName": "string",
  "LastName": "string",
  "MiddleName": "string or null",
  "EmailAddress": "string",
  "Suffix": "string or null",
  "Prefix": "string or null",
  "IsActive": true,
  "FacetInstances": [
    {
      "FacetDefinitionId": "guid-string",
      "Values": ["value1", "value2"]
    }
  ],
  "FieldInstances": [],
  "HybridListInstanceItems": [],
  "CreatedAt": "2024-01-15T10:30:00Z",
  "UpdatedAt": "2024-01-15T10:30:00Z"
}
```

### All Supported Event Types

| Category | Before Event | After Event | Async Event |
|----------|--------------|-------------|-------------|
| Organization | `BeforeOrganizationCreated` | `AfterOrganizationCreated` | `AsyncOrganizationCreated` |
| Organization | `BeforeOrganizationUpdated` | `AfterOrganizationUpdated` | `AsyncOrganizationUpdated` |
| Organization | `BeforeOrganizationDeleted` | `AfterOrganizationDeleted` | `AsyncOrganizationDeleted` |
| Person | `BeforePersonCreated` | `AfterPersonCreated` | `AsyncPersonCreated` |
| Person | `BeforePersonUpdated` | `AfterPersonUpdated` | `AsyncPersonUpdated` |
| Person | `BeforePersonDeleted` | `AfterPersonDeleted` | `AsyncPersonDeleted` |
| FacetDefinition | `BeforeFacetDefinitionCreated` | `AfterFacetDefinitionCreated` | - |
| FacetDefinition | `BeforeFacetDefinitionUpdated` | `AfterFacetDefinitionUpdated` | - |
| FacetDefinition | `BeforeFacetDefinitionDeleted` | `AfterFacetDefinitionDeleted` | - |
| FieldDefinition | `BeforeFieldDefinitionCreated` | `AfterFieldDefinitionCreated` | - |
| FieldDefinition | `BeforeFieldDefinitionUpdated` | `AfterFieldDefinitionUpdated` | - |
| FieldDefinition | `BeforeFieldDefinitionDeleted` | `AfterFieldDefinitionDeleted` | - |
| Secret | `BeforeSecretCreated` | `AfterSecretCreated` | - |
| Secret | `BeforeSecretUpdated` | `AfterSecretUpdated` | - |
| Secret | `BeforeSecretDeleted` | `AfterSecretDeleted` | - |

### Event Timing Semantics

| Timing | When Executed | Can Cancel | Can Modify | Database State |
|--------|---------------|------------|------------|----------------|
| **Before** | Before database save | Yes (via `CancelAction()`) | Facets, Fields, HybridLists, Entitlements | Entity not yet persisted |
| **After** | After database save | No | Read-only (changes ignored) | Entity persisted |
| **Async** | Background (fire-and-forget) | No | Read-only | Entity persisted |

---

## Dynamic Endpoints

Dynamic endpoints receive HTTP request context and can control the HTTP response.

### Input: Data.request

The `Data.request` object contains all information about the incoming HTTP request:

```json
{
  "Request": {
    "method": "GET",
    "path": "/api/dynamic/v1/my-endpoint",
    "queryString": "?param1=value1&param2=value2",
    "query": {
      "param1": "value1",
      "param2": "value2"
    },
    "headers": {
      "authorization": "Bearer eyJ...",
      "content-type": "application/json",
      "host": "localhost:7000",
      "user-agent": "Mozilla/5.0..."
    },
    "body": {
      "key": "value"
    },
    "user": {
      "isAuthenticated": true,
      "name": "john@example.com",
      "principalId": "user:john@example.com",
      "roles": ["user", "admin"]
    }
  }
}
```

### Request Properties Reference

| Property | Type | Description |
|----------|------|-------------|
| `Data.request.method` | string | HTTP method: `GET`, `POST`, `PUT`, `PATCH`, `DELETE` |
| `Data.request.path` | string | Full request path including `/api/dynamic/` prefix |
| `Data.request.queryString` | string | Raw query string including `?` (e.g., `?id=123&name=test`) |
| `Data.request.query` | object | Parsed query parameters as key-value pairs |
| `Data.request.headers` | object | HTTP headers with **lowercase** keys |
| `Data.request.body` | object/null | Parsed JSON body (for POST/PUT/PATCH requests) |
| `Data.request.user` | object | Authenticated user information |

### User Properties Reference

| Property | Type | Description |
|----------|------|-------------|
| `Data.request.user.isAuthenticated` | boolean | `true` if user is authenticated |
| `Data.request.user.name` | string | User's identity name (usually email) |
| `Data.request.user.principalId` | string | Full principal ID (e.g., `user:john@example.com`) |
| `Data.request.user.roles` | array | List of user's assigned roles |

### Output: Response Control

Control the HTTP response by setting these special properties on the `Data` object:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `Data._payload` | any | `null` | Response body (will be JSON serialized) |
| `Data._statusCode` | number | `200` | HTTP status code |
| `Data._redirect` | string | `null` | URL to redirect to (returns 302) |
| `Data._contentType` | string | `application/json` | Response Content-Type header |

### Example: Complete Dynamic Endpoint

```jyro
# GET /api/dynamic/v1/organization-summary?orgId=xxx

# Validate required parameter
if Data.request.query.orgId == null then
    Data._payload = { "error": "orgId parameter is required" }
    Data._statusCode = 400
    return
end

# Check authentication
if Data.request.user.isAuthenticated == false then
    Data._payload = { "error": "Authentication required" }
    Data._statusCode = 401
    return
end

# Get organization data
var org = GetOrganizationById(Data.request.query.orgId)

if org == null then
    Data._payload = { "error": "Organization not found" }
    Data._statusCode = 404
    return
end

# Get facet data
var facets = GetOrganizationFacetInstances(org.Id)

# Build response
Data._payload = {
    "id": org.Id,
    "name": org.Name,
    "description": org.Description,
    "isActive": org.IsActive,
    "facetCount": facets.length,
    "requestedBy": Data.request.user.name,
    "requestedAt": Now()
}
Data._statusCode = 200
```

### Example: POST with Body Validation

```jyro
# POST /api/dynamic/v1/notifications/send

# Validate request body
if Data.request.body == null then
    Data._payload = { "error": "Request body is required" }
    Data._statusCode = 400
    return
end

if Data.request.body.userId == null then
    Data._payload = { "error": "userId is required" }
    Data._statusCode = 400
    return
end

if Data.request.body.message == null then
    Data._payload = { "error": "message is required" }
    Data._statusCode = 400
    return
end

# Send notification
var success = SendNotification(
    Data.request.body.userId,
    Data.request.body.title or "Notification",
    Data.request.body.message,
    Data.request.body.type or "Info",
    Data.request.body.link
)

if success then
    Data._payload = { "success": true, "message": "Notification sent" }
    Data._statusCode = 200
else
    Data._payload = { "success": false, "message": "Failed to send notification" }
    Data._statusCode = 500
end
```

### Example: Redirect Response

```jyro
# GET /api/dynamic/v1/redirect-example

# Set redirect URL (returns HTTP 302)
Data._redirect = "/dashboard"

# Note: _payload is ignored when _redirect is set
```

### Example: Custom Content Type

```jyro
# GET /api/dynamic/v1/plain-text

Data._payload = "Hello, this is plain text!"
Data._contentType = "text/plain"
Data._statusCode = 200
```

---

## Best Practices

### Event Hooks

1. **Always check for null** before accessing nested properties
2. **Use Before hooks** for validation and data enrichment
3. **Use After hooks** for notifications and side effects
4. **Use CancelAction()** only in Before hooks when validation fails
5. **Keep scripts simple** - complex logic belongs in the domain layer

### Dynamic Endpoints

1. **Validate all input** - check query parameters and body data
2. **Check authentication** when required (`Data.request.user.isAuthenticated`)
3. **Return appropriate status codes** - 400 for bad input, 401 for auth, 404 for not found
4. **Always set _payload** even for errors
5. **Log important operations** with the `Log()` function

### Common Patterns

**Null-safe property access:**
```jyro
# Safe way to access potentially null properties
var name = Data.organization.Name or "Unknown"
var desc = Data.organization.Description or ""
```

**Check if update changed a specific field:**
```jyro
# In update events
if Data.previous != null and Data.current.Name != Data.previous.Name then
    Log("Information", "Name was changed")
end
```

**Build response objects:**
```jyro
# Dynamic endpoint response
Data._payload = {
    "success": true,
    "data": {
        "id": result.Id,
        "name": result.Name
    },
    "meta": {
        "requestedBy": Data.request.user.name,
        "timestamp": Now()
    }
}
```

---

## Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| `Data.organization is null` | Wrong event type or entity not in context | Check event type matches entity |
| `Data.previous is null` | Async event or create/delete event | Use sync events for previous state |
| `Data.request is null` | Script is event hook, not dynamic endpoint | Check script context |
| `_payload not returned` | Script error or early return | Check logs for errors |
| Changes to entity ignored | After hook or protected property | Use Before hook for modifications |

### Debugging Tips

1. Use `Log("Debug", ...)` to inspect object values
2. Check application logs for script execution errors
3. Verify the event type matches your expectations
4. Test with simple scripts first, then add complexity
