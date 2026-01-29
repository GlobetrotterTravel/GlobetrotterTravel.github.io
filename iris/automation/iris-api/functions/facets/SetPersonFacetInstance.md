---
layout: default
title: SetPersonFacetInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# SetPersonFacetInstance

Sets or updates a facet instance on a person.

## Syntax

```jyro
SetPersonFacetInstance(personId, facetDefinitionId, values)
```

## Parameters

- **personId** (string): The GUID of the person
- **facetDefinitionId** (string): The GUID of the facet definition
- **values** (array): Array of string values to set

## Returns

- **boolean**: true if successful, false otherwise

## Description

Smart detection: Works in both "Before" hooks (in-memory) and "After" hooks (persisted):
- If person exists in database: Fetches, modifies, and saves
- If person is in-memory only: Modifies Data.Person in execution context

Creates a new facet instance if one doesn't exist for the given definition, or updates the existing instance's values.

## Examples

```jyro
# Set facet with single value
var levelFacetId = GetFacetDefinitionByName("Experience Level")
SetPersonFacetInstance(
    Data.Person.Id.Value,
    levelFacetId.id,
    ["Senior"]
)
```

```jyro
# Set facet with multiple values (skills)
var skillsFacet = GetFacetDefinitionByName("Skills")
var success = SetPersonFacetInstance(
    personId,
    skillsFacet.id,
    ["C#", "TypeScript", "SQL", "Azure"]
)

if success then
    Log("Information", "Skills updated successfully")
end
```

```jyro
# Set facet in BeforePersonCreated hook
var departmentFacet = GetFacetDefinitionByName("Department")
SetPersonFacetInstance(
    Data.Person.Id.Value,
    departmentFacet.id,
    ["Engineering"]
)
# The person will be created with this facet already set
```

```jyro
# Conditional facet assignment based on email domain
if EndsWith(Data.Person.EmailAddress, "@example.com") then
    var roleFacet = GetFacetDefinitionByName("Role")
    SetPersonFacetInstance(
        Data.Person.Id.Value,
        roleFacet.id,
        ["Employee"]
    )
else
    var roleFacet = GetFacetDefinitionByName("Role")
    SetPersonFacetInstance(
        Data.Person.Id.Value,
        roleFacet.id,
        ["Contractor"]
    )
end
```

```jyro
# Update existing facet value
var statusFacet = GetFacetDefinitionByName("Employment Status")
var currentStatus = GetPersonFacetInstance(personId, statusFacet.id)

if currentStatus != null then
    # Update to new status
    SetPersonFacetInstance(personId, statusFacet.id, ["Active"])
    Log("Information", "Updated employment status from " + currentStatus.values[0] + " to Active")
end
```

## Notes

- The facet definition must exist in the tenant before calling this function
- Values must comply with the facet definition's constraints (allowed values, max values, etc.)
- Aggregate IDs (Id, TenantId) are protected and cannot be modified by scripts
- If the facet instance already exists, its values are replaced with the new values
- For multi-value facets, pass all values in the array; existing values are replaced, not appended
