---
layout: default
title: Event hooks
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 20
permalink: /iris/automation/event-hooks/
---

# Event Hooks

Event hooks allow you to execute custom Jyro scripts when domain events occur in the platform. You can validate data, enrich records, send notifications, and trigger integrations - all without modifying the application code.

## What Are Event Hooks?

An event hook is a Jyro script that runs automatically when a specific event occurs. For example:

- When an organization is created, send a welcome notification
- When a person is updated, validate that required facets are set
- When a record is deleted, log an audit entry

Event hooks integrate seamlessly with the domain model, giving your scripts access to the entity data being created, updated, or deleted.

## Hook Timing Types

Every event has three timing variants that determine when your script runs:

| Timing | Runs | Can Modify | Can Cancel | Use For |
|--------|------|-----------|-----------|---------|
| **Before** | Before the record is saved | Yes (facets, fields) | Yes | Validation, data enrichment |
| **After** | After the record is saved | No (read-only) | No | Notifications, side effects |
| **Async** | In background queue | No (read-only) | No | Long-running tasks, integrations |

### Before Hooks

Before hooks run synchronously before the record is persisted. They can:

- **Modify data**: Set facet values, field values, or hybrid list items
- **Cancel the operation**: Call `CancelAction(reason)` to abort the save

```jyro
BeforeOrganizationCreated:
  # Validate required data
  if Data.organization.description == null then
    CancelAction("Description is required for all organizations")
  end

  # Enrich with default facet values
  var domainFacetId = GetFacetDefinitionByName("Primary Domain")
  SetOrganizationFacetInstance(
    Data.organization.id,
    domainFacetId,
    ["pending-configuration.example.com"]
  )
```

> ⚠ Before hooks are synchronous and block the request. Keep them fast to avoid impacting user experience.

### After Hooks

After hooks run synchronously after the record is saved. The data is already persisted, so you cannot modify it or cancel the operation.

```jyro
AfterOrganizationCreated:
  # Send notification
  SendNotification(
    "admin@example.com",
    "New Organization",
    Data.organization.name + " was created.",
    "Information",
    "/organizations/" + Data.organization.id
  )

  # Log for audit
  Log("Information", "Organization created: " + Data.organization.name)
```

### Async Hooks

Async hooks are queued for background processing. They don't block the request and are ideal for long-running operations.

```jyro
AsyncOrganizationCreated:
  # This runs in the background - doesn't block the request
  var children = GetChildOrganizations(Data.organization.id)

  foreach child in children do
    # Update child organizations
    Log("Information", "Processing child: " + child.name)
  end
```

## Available Events

### Organization Events

| Event | Timing Variants |
|-------|----------------|
| OrganizationCreated | Before, After, Async |
| OrganizationUpdated | Before, After, Async |
| OrganizationDeleted | Before, After, Async |

### Person Events

| Event | Timing Variants |
|-------|----------------|
| PersonCreated | Before, After, Async |
| PersonUpdated | Before, After, Async |
| PersonDeleted | Before, After, Async |

### Definition Events

Facet, Field, Entitlement, Role, HybridList, Set, and Property definitions all support the same event patterns:

- `Before[Entity]Created`, `After[Entity]Created`, `Async[Entity]Created`
- `Before[Entity]Updated`, `After[Entity]Updated`, `Async[Entity]Updated`
- `Before[Entity]Deleted`, `After[Entity]Deleted`, `Async[Entity]Deleted`

## Property Protection

Before hooks can only modify specific properties. This protects core entity data from accidental corruption:

| Can Modify | Cannot Modify |
|------------|---------------|
| FacetInstances | Id |
| FieldInstances | TenantId |
| EntitlementInstances | Name |
| HybridListInstanceItems | Description |
| | IsActive |
| | CreatedAt, UpdatedAt |

Example:

```jyro
BeforeOrganizationCreated:
  # These work - modifying allowed properties
  SetOrganizationFacetInstance(Data.organization.id, facetId, values)

  # These are ignored - protected properties
  # Data.organization.name = "New Name"  # Ignored
  # Data.organization.isActive = false   # Ignored
```

## Priority and Execution Order

When multiple hooks are registered for the same event, they execute in priority order:

- **Priority 0-999**: Lower numbers run first
- **Default priority**: 100

Example with three hooks:
1. Validation hook (priority 10) - runs first
2. Enrichment hook (priority 50) - runs second
3. Notification hook (priority 100) - runs last

> 🛈 If a Before hook calls `CancelAction()`, subsequent hooks do not run and the operation is aborted.

## Creating an Event Hook

1. Navigate to `Administration` > `Scripting` > `Event Hooks`
2. Click `+ Create Event Hook`
3. Fill in the details:
   - **Name**: Descriptive name for the hook
   - **Event Type**: Select the event (e.g., `AfterOrganizationCreated`)
   - **Priority**: Execution order (default: 100)
   - **Script Content**: Your Jyro script
   - **Is Active**: Enable/disable the hook
4. Click `Save`

## Common Use Cases

### Validation Before Save

```jyro
BeforePersonCreated:
  # Require email address
  if Data.person.emailAddress == null or Data.person.emailAddress == "" then
    CancelAction("Email address is required")
  end

  # Validate email domain
  var parts = Data.person.emailAddress.split("@")
  var domain = parts[1]

  var allowedDomains = ["company.com", "partner.com"]
  var isAllowed = false

  foreach allowed in allowedDomains do
    if domain == allowed then
      isAllowed = true
    end
  end

  if isAllowed == false then
    CancelAction("Email domain " + domain + " is not allowed")
  end
```

### Auto-Populate Facets

```jyro
BeforeOrganizationCreated:
  # Set default region based on name
  var regionFacetId = GetFacetDefinitionByName("Region")

  if Data.organization.name.contains("EMEA") then
    SetOrganizationFacetInstance(Data.organization.id, regionFacetId, ["Europe"])
  elif Data.organization.name.contains("APAC") then
    SetOrganizationFacetInstance(Data.organization.id, regionFacetId, ["Asia Pacific"])
  else
    SetOrganizationFacetInstance(Data.organization.id, regionFacetId, ["North America"])
  end
```

### Send Notifications

```jyro
AfterPersonCreated:
  # Welcome the new person
  SendNotification(
    Data.person.emailAddress,
    "Welcome to the Platform",
    "Hello " + Data.person.firstName + ", your account is ready.",
    "Success",
    "/profile"
  )

  # Notify administrators
  BroadcastNotification(
    "New Person Added",
    Data.person.firstName + " " + Data.person.lastName + " has joined.",
    "Information",
    "/persons/" + Data.person.id,
    true  # Tenant-scoped
  )
```

### Audit Logging

```jyro
AfterOrganizationDeleted:
  # Create audit notepad
  CreateNotepad({
    event: "OrganizationDeleted",
    organizationId: Data.organization.id,
    organizationName: Data.organization.name,
    deletedAt: Now(),
    deletedBy: GetCurrentUser().name
  })

  Log("Warning", "Organization deleted: " + Data.organization.name)
```

### Compare Before and After

```jyro
AfterOrganizationUpdated:
  # Check if status changed
  if Data.previous != null then
    if Data.current.isActive != Data.previous.isActive then
      if Data.current.isActive == true then
        Log("Information", Data.current.name + " was activated")
      else
        Log("Warning", Data.current.name + " was deactivated")
      end
    end
  end
```

## Debugging Event Hooks

### Use Logging

Add `Log()` statements to trace execution:

```jyro
AfterOrganizationCreated:
  Log("Debug", "Hook started for: " + Data.organization.name)

  # Your logic here
  Log("Debug", "Processing step 1")

  # More logic
  Log("Debug", "Hook completed")
```

### Use Notepads

Capture state for later analysis:

```jyro
BeforePersonCreated:
  CreateNotepad({
    hookName: "BeforePersonCreated",
    timestamp: Now(),
    inputData: Data.person
  })
```

### Check Execution History

View hook execution results in `Administration` > `Scripting` > `Execution History`. You can see:

- Success or failure status
- Execution time
- Log lines generated
- Notepads created
- Error messages (if failed)

## Best Practices

1. **Keep Before hooks fast**: They block the request
2. **Use Async for heavy work**: Long-running tasks should be async
3. **Log strategically**: Include enough context to debug issues
4. **Handle null values**: Always check if data exists before accessing properties
5. **Use descriptive names**: Name hooks clearly (e.g., "Validate Required Fields", "Send Welcome Email")
6. **Set appropriate priorities**: Validation hooks should run before enrichment hooks
7. **Test thoroughly**: Use the Scripting Dashboard to test before enabling

## Next Steps

- [Data Context](data-context) - Understand what data is available in hooks
- [Log Lines](log-lines) - Debug your hooks effectively
- [Notepads](notepads) - Store output for later retrieval
- [Event Queue](event-queue) - Monitor async hook execution
