---
layout: default
title: GetAllOrganizations
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllOrganizations

Retrieves all organizations in the current tenant.

## Syntax

```jyro
GetAllOrganizations()
```

## Parameters

None

## Returns

- **array**: Array of all organization objects

## Description

Returns an array containing all organizations in the tenant. Each element is a complete organization object with all properties (id, name, description, facetInstances, fieldInstances, etc.).

## Examples

```jyro
# Get count of all organizations
var allOrgs = GetAllOrganizations()
Log("Information", "Total organizations: " + Length(allOrgs))
```

```jyro
# List all organization names
var organizations = GetAllOrganizations()
foreach org in organizations do
    Log("Information", "Organization: " + org.name)
end
```

```jyro
# Find active organizations
var allOrgs = GetAllOrganizations()
var activeCount = 0

foreach org in allOrgs do
    if org.isActive then
        activeCount = activeCount + 1
    end
end

Log("Information", "Active organizations: " + activeCount)
```

```jyro
# Delete all test organizations
var allOrgs = GetAllOrganizations()

foreach org in allOrgs do
    if org.name.contains("Test") or org.name.contains("Demo") then
        var deleted = DeleteOrganization(org.id)
        if deleted then
            Log("Information", "Deleted test org: " + org.name)
        end
    end
end
```
