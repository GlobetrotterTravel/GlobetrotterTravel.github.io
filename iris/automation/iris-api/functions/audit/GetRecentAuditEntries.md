---
layout: default
title: GetRecentAuditEntries
parent: Iris API functions
has_children: false
has_toc: false
---

# GetRecentAuditEntries

Retrieves the most recent audit entries for the current tenant.

## Syntax

```jyro
GetRecentAuditEntries()
GetRecentAuditEntries(limit)
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `limit` | number | No | Maximum number of entries to return (default: 50, max: 1000) |

## Returns

An array of audit entry objects ordered by `occurredAt` (most recent first). Each object has the following properties:

| Property | Type | Description |
|----------|------|-------------|
| `id` | string | Unique identifier for the audit entry (GUID) |
| `aggregateType` | string | Type of entity that was changed (e.g., "Organization", "Person") |
| `operationType` | string | Type of operation ("Created", "Updated", "Deleted") |
| `changeCount` | number | Number of property changes in this audit entry |
| `principal` | string | The principal ID who made the change (e.g., "user:john@example.com") |
| `principalDisplayName` | string | Human-readable name for the principal (may be null) |
| `occurredAt` | string | ISO 8601 timestamp when the change occurred |
| `subEntityType` | string | Sub-entity type if applicable (e.g., "FacetInstance") (may be null) |
| `subEntityDisplayName` | string | Human-readable sub-entity name (may be null) |

Returns an empty array if no audit entries are found or if an error occurs.

## Description

Retrieves audit trail entries for the current tenant, providing a summary of recent changes to domain entities. This function is useful for:

- Building audit dashboards
- Displaying recent activity feeds
- Monitoring system changes
- Compliance and governance reporting
- Debugging and troubleshooting

The function returns minimal audit entry data optimized for list views. For full audit details including all property changes, use the Audit REST API.

## Examples

### Get Recent Activity

```jyro
var entries = GetRecentAuditEntries()

Log("Information", "Found " + entries.length + " recent audit entries")

foreach entry in entries do
    Log("Information", entry.aggregateType + " " + entry.operationType + " by " + entry.principalDisplayName)
end
```

### Limit Results

```jyro
# Get only the last 10 changes
var entries = GetRecentAuditEntries(10)

foreach entry in entries do
    Log("Information", "[" + entry.occurredAt + "] " + entry.aggregateType + ": " + entry.operationType)
end
```

### Filter by Entity Type

```jyro
var entries = GetRecentAuditEntries(100)

# Count organization changes
var orgChanges = 0
foreach entry in entries do
    if entry.aggregateType == "Organization" then
        orgChanges = orgChanges + 1
        Log("Information", "Org change: " + entry.operationType + " at " + entry.occurredAt)
    end
end

Log("Information", "Total organization changes: " + orgChanges)
```

### Build Activity Feed

```jyro
var entries = GetRecentAuditEntries(20)

Data.activities = []

foreach entry in entries do
    var activity = {
        "id": entry.id,
        "type": entry.aggregateType,
        "action": entry.operationType,
        "actor": entry.principalDisplayName,
        "timestamp": entry.occurredAt,
        "changes": entry.changeCount
    }

    # Build human-readable description
    if entry.operationType == "Created" then
        activity.description = entry.principalDisplayName + " created a new " + entry.aggregateType
    else if entry.operationType == "Updated" then
        activity.description = entry.principalDisplayName + " updated " + entry.aggregateType + " (" + entry.changeCount + " changes)"
    else if entry.operationType == "Deleted" then
        activity.description = entry.principalDisplayName + " deleted " + entry.aggregateType
    end

    Data.activities.add(activity)
end

Data._payload = Data.activities
Data._statusCode = 200
```

### Audit Summary Report

```jyro
var entries = GetRecentAuditEntries(500)

# Build summary statistics
var stats = {
    "total": entries.length,
    "byType": {},
    "byOperation": {},
    "byUser": {}
}

foreach entry in entries do
    # Count by aggregate type
    if stats.byType[entry.aggregateType] == null then
        stats.byType[entry.aggregateType] = 0
    end
    stats.byType[entry.aggregateType] = stats.byType[entry.aggregateType] + 1

    # Count by operation
    if stats.byOperation[entry.operationType] == null then
        stats.byOperation[entry.operationType] = 0
    end
    stats.byOperation[entry.operationType] = stats.byOperation[entry.operationType] + 1

    # Count by user
    var user = entry.principalDisplayName
    if user == null then
        user = entry.principal
    end
    if stats.byUser[user] == null then
        stats.byUser[user] = 0
    end
    stats.byUser[user] = stats.byUser[user] + 1
end

Log("Information", "Audit Summary: " + stats.total + " entries")
Log("Information", "Types: " + stats.byType)
Log("Information", "Operations: " + stats.byOperation)

Data._payload = stats
Data._statusCode = 200
```

### Monitor Specific User Activity

```jyro
var entries = GetRecentAuditEntries(200)
var targetUser = "john@example.com"

var userEntries = []
foreach entry in entries do
    # Check both principal and display name
    if entry.principal == "user:" + targetUser or entry.principalDisplayName == targetUser then
        userEntries.add(entry)
    end
end

if userEntries.length > 0 then
    Log("Information", "Found " + userEntries.length + " actions by " + targetUser)
    foreach e in userEntries do
        Log("Information", "  " + e.occurredAt + ": " + e.operationType + " " + e.aggregateType)
    end
else
    Log("Information", "No recent activity found for " + targetUser)
end
```

### Render Activity Timeline

```jyro
var entries = GetRecentAuditEntries(25)
var tenant = GetCurrentTenant()

Data.model = {
    "tenantName": tenant.name,
    "entries": entries,
    "generatedAt": Now()
}

Data._payload = RenderTemplate("audit-timeline", Data.model)
Data._contentType = "text/html"
Data._statusCode = 200
```

## Notes

- Entries are always ordered by `occurredAt` descending (most recent first)
- The `principalDisplayName` may be null for system operations or deleted users
- The limit is capped at 1000 entries to prevent performance issues
- Returns an empty array (not null) if no entries exist or an error occurs
- For full audit details with all property changes, use the Audit REST API at `/api/v1/audit/{id}`
- Audit entries are tenant-scoped; only entries for the current tenant are returned
