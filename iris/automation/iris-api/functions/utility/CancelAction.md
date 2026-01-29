---
layout: default
title: CancelAction
parent: Iris API functions
has_children: false
has_toc: false
---

# CancelAction

Cancels the current operation and prevents it from completing. Only works in "Before" event hooks.

## Syntax

```jyro
CancelAction(reason)
```

## Parameters

- **reason** (string): The reason for cancellation (included in error message)

## Returns

- **void**: This function does not return (stops script execution)

## Description

Immediately halts the current operation and returns an error to the caller. This function is only effective in "Before" event hooks (BeforeOrganizationCreated, BeforePersonUpdated, etc.). In "After" hooks, this function has no effect since the operation has already been committed to the database.

When called, the operation is aborted and the reason is returned as an error message to the API caller.

## Examples

```jyro
# Cancel if required field is missing
if Data.Organization.Description == null or Data.Organization.Description == "" then
    Log("Warning", "Organization creation cancelled: missing description")
    CancelAction("Organization description is required")
end
```

```jyro
# Cancel if validation fails
var domainFacet = GetFacetDefinitionByName("Primary Domain")
var domain = GetOrganizationFacetInstance(Data.Organization.Id.Value, domainFacet.Id)

if domain == null then
    CancelAction("Primary domain must be set before creating organization")
end
```

```jyro
# Cancel based on business rule
if Data.Organization.Name.contains("Test") or Data.Organization.Name.contains("Demo") then
    CancelAction("Test and demo organizations are not allowed in production")
end
```

```jyro
# Cancel if duplicate detected
var existing = GetAllOrganizations()
foreach org in existing do
    if org.name == Data.Organization.Name and org.id != Data.Organization.Id.Value then
        Log("Warning", "Duplicate organization name detected: " + Data.Organization.Name)
        CancelAction("An organization with this name already exists")
    end
end
```

```jyro
# Cancel with secret validation
var requiredApiKey = GetSecret("RequiredApiKey")
if requiredApiKey == null then
    Log("Error", "Required API key secret not found")
    CancelAction("System misconfiguration: missing required API key")
end
```
