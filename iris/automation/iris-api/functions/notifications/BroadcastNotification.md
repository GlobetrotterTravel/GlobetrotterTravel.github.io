---
layout: default
title: BroadcastNotification
parent: Iris API functions
has_children: false
has_toc: false
---

# BroadcastNotification

Broadcasts a notification to all active users in the tenant (or all tenants).

## Syntax

```jyro
BroadcastNotification(title, message, type, link, tenantScoped)
```

## Parameters

- **title** (string): Notification title
- **message** (string): Notification message body
- **type** (string): Notification type: "Info", "Success", "Warning", or "Error"
- **link** (string, optional): Navigation link (can be null)

## Returns

- **boolean**: true on success

## Description

Broadcasts a notification to all active users with a UPN in the tenant. Creates individual notification records for each user, then pushes via SignalR.

Execution is fire-and-forget and won't block script execution.

## Examples

```jyro
# Broadcast to all users in tenant
BroadcastNotification(
    "System Maintenance",
    "Scheduled maintenance tonight at 10pm. System will be unavailable for 1 hour.",
    "Warning",
    null
)
```

```jyro
# Broadcast organization update
BroadcastNotification(
    "Organization Updated",
    Data.Organization.Name + " has been updated. Check it out!",
    "Info",
    "/organizations/" + Data.Organization.Id
)
```

```jyro
# Global broadcast (all tenants)
BroadcastNotification(
    "Organization Deleted",
    "ALERT: Organization '" + Data.Organization.Name + "' was permanently deleted.",
    "Warning",
    null
)
Log("Warning", "Global deletion alert sent")
```

```jyro
# Success broadcast after bulk operation
var processedCount = 50
BroadcastNotification(
    "Bulk Import Complete",
    "Successfully imported " + processedCount + " organizations.",
    "Success",
    "/organizations"
)
```
