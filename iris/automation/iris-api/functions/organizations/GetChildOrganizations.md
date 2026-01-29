---
layout: default
title: GetChildOrganizations
parent: Iris API functions
has_children: false
has_toc: false
---

# GetChildOrganizations

Retrieves all child organizations of a given parent organization.

## Syntax

```jyro
GetChildOrganizations(parentOrganizationId)
```

## Parameters

- **parentOrganizationId** (string): The GUID of the parent organization

## Returns

- **array**: Array of organization objects, or empty array if none found or error

## Description

Returns an array of all organizations that have the specified organization as their parent. Each element in the array is a complete organization object with all properties (id, name, description, etc.).

## Examples

```jyro
# Get children of a specific organization
var children = GetChildOrganizations(Data.Organization.Id.Value)
if Length(children) > 0 then
    Log("Information", "Found " + Length(children) + " child organizations")
end
```

```jyro
# Iterate through children
var parentId = "a1b2c3d4-5678-90ab-cdef-1234567890ab"
var children = GetChildOrganizations(parentId)
foreach child in children do
    Log("Information", "Child: " + child.name + " (ID: " + child.id + ")")
end
```

```jyro
# Get children and delete them
var children = GetChildOrganizations(orgId)
foreach child in children do
    var deleted = DeleteOrganization(child.id)
    if deleted then
        Log("Information", "Deleted child organization: " + child.name)
    end
end
```

```jyro
# Count active vs inactive children
var children = GetChildOrganizations(parentOrgId)
var activeCount = 0
var inactiveCount = 0

foreach child in children do
    if child.isActive then
        activeCount = activeCount + 1
    else
        inactiveCount = inactiveCount + 1
    end
end

Log("Information", "Active children: " + activeCount)
Log("Information", "Inactive children: " + inactiveCount)
```
