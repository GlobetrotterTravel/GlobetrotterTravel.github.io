---
layout: default
title: Iris API
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 20
has_children: true
has_toc: false
permalink: /iris/automation/iris-api/
---

# Iris API
{: .no_toc }

This documentation covers the Jyro scripting API available in Iris for Event Hooks, Dynamic Endpoints, and Ad-hoc Scripts.

## Getting Started

### Key Concepts

1. **Event Hooks** - Scripts triggered by domain events (e.g., `BeforeOrganizationCreated`, `AfterPersonUpdated`)
2. **Dynamic Endpoints** - Custom REST API endpoints powered by Jyro scripts
3. **Ad-hoc Scripts** - Administrative scripts executed on-demand

### Essential Reading

| Document | Description |
|----------|-------------|
| [Data Context Reference](data-context) | Structure of the `Data` object passed to scripts |
| [Host Functions](functions/README) | All available host functions for interacting with Iris |

## Data Context

Every script receives a `Data` object containing context-specific information:

### Event Hooks

- **Create/Delete Events**: Access entity via `Data.{entityType}` (e.g., `Data.organization`, `Data.person`)
- **Update Events**: Access both states via `Data.Current` and `Data.Previous`

```jyro
# BeforeOrganizationCreated
Log("Information", "Creating: " + Data.organization.Name)

# AfterPersonUpdated
if Data.Current.EmailAddress != Data.Previous.EmailAddress then
    Log("Information", "Email changed")
end
```

### Dynamic Endpoints

- **Request Data**: `Data.request.method`, `Data.request.query`, `Data.request.body`, `Data.request.user`
- **Response Control**: `Data._payload`, `Data._statusCode`, `Data._redirect`

```jyro
# GET /api/dynamic/v1/hello
Data._payload = { "message": "Hello, " + Data.request.user.name }
Data._statusCode = 200
```

See the [Data Context Reference](data-context) for complete details.

## Quick Reference

### Event Types

| Entity | Before | After | Async |
|--------|--------|-------|-------|
| Organization | `BeforeOrganizationCreated/Updated/Deleted` | `AfterOrganizationCreated/Updated/Deleted` | `AsyncOrganizationCreated/Updated/Deleted` |
| Person | `BeforePersonCreated/Updated/Deleted` | `AfterPersonCreated/Updated/Deleted` | `AsyncPersonCreated/Updated/Deleted` |

### Common Host Functions

| Function | Description |
|----------|-------------|
| `Log(level, message)` | Write to application logs |
| `CancelAction(reason)` | Cancel operation (Before hooks only) |
| `GetOrganizationById(id)` | Get organization by ID |
| `GetAllOrganizations()` | Get all organizations |
| `SendNotification(userId, title, message, type, link)` | Send push notification |
| `GetSecret(name)` | Get decrypted secret value |

See [Host Functions](functions/README) for the complete list.

## External Resources

- [Jyro Language Documentation](https://meschsystems.github.io/jyro/) - Complete Jyro syntax reference
