---
layout: default
title: SetOrganizationFacetInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# SetOrganizationFacetInstance

Sets or updates a facet instance on an organization.

## Syntax

```jyro
SetOrganizationFacetInstance(organizationId, facetDefinitionId, values)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **facetDefinitionId** (string): The GUID of the facet definition
- **values** (array): Array of string values to set

## Returns

- **boolean**: true if successful, false otherwise

## Description

Smart detection: Works in both "Before" hooks (in-memory) and "After" hooks (persisted):
- If organization exists in database: Fetches, modifies, and saves
- If organization is in-memory only: Modifies Data.Organization in execution context

Creates a new facet instance if one doesn't exist for the given definition, or updates the existing instance's values.

## Examples

```jyro
# Set facet with single value
var domainFacetId = GetFacetDefinitionByName("Primary Domain")
SetOrganizationFacetInstance(
    Data.Organization.Id.Value,
    domainFacetId.id,
    ["example.com"]
)
```

```jyro
# Set facet with multiple values
var tagsFacet = GetFacetDefinitionByName("Tags")
var success = SetOrganizationFacetInstance(
    orgId,
    tagsFacet.id,
    ["Enterprise", "Customer", "US-West"]
)

if success then
    Log("Information", "Tags updated successfully")
end
```

```jyro
# Set facet in BeforeOrganizationCreated hook
var industryFacet = GetFacetDefinitionByName("Industry")
SetOrganizationFacetInstance(
    Data.Organization.Id.Value,
    industryFacet.id,
    ["Technology"]
)
# The organization will be created with this facet already set
```

```jyro
# Update existing facet value
var sizeFacet = GetFacetDefinitionByName("Company Size")
var currentSize = GetOrganizationFacetInstance(orgId, sizeFacet.id)

if currentSize != null then
    # Update to new size
    SetOrganizationFacetInstance(orgId, sizeFacet.id, ["Large"])
    Log("Information", "Updated company size from " + currentSize.values[0] + " to Large")
end
```
