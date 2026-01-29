---
layout: default
title: GetOrganizationFacetInstances
parent: Iris API functions
has_children: false
has_toc: false
---

# GetOrganizationFacetInstances

Retrieves all facet instances for an organization as an array.

## Syntax

```jyro
GetOrganizationFacetInstances(organizationId)
```

## Parameters

- **organizationId** (string): The GUID of the organization

## Returns

- **array**: Array of facet instance objects, each with { facetDefinitionId, values }

## Description

Returns an array of all facet instances attached to an organization. Each element contains the facet definition ID and the array of values for that facet.

## Examples

```jyro
# Get all facet instances
var facets = GetOrganizationFacetInstances(Data.Current.Id.Value)

if facets != null and Length(facets) > 0 then
    Log("Information", "Organization has " + Length(facets) + " facet instances")

    foreach facet in facets do
        Log("Information", "  Facet ID: " + facet.facetDefinitionId)
        Log("Information", "  Values: " + Length(facet.values) + " value(s)")
    end
end
```

```jyro
# Check if organization has specific facet
var facets = GetOrganizationFacetInstances(orgId)
var domainFacetDef = GetFacetDefinitionByName("Primary Domain")
var hasPrimaryDomain = false

foreach facet in facets do
    if facet.facetDefinitionId == domainFacetDef.id then
        hasPrimaryDomain = true
        Log("Information", "Has Primary Domain: " + facet.values[0])
    end
end

if not hasPrimaryDomain then
    Log("Warning", "Organization does not have a Primary Domain facet")
end
```

```jyro
# Log all facets with their definition names
var facets = GetOrganizationFacetInstances(orgId)
var allFacetDefs = GetAllFacetDefinitions()

foreach facet in facets do
    # Find the facet definition name
    foreach facetDef in allFacetDefs do
        if facetDef.id == facet.facetDefinitionId then
            Log("Information", facetDef.name + ": " + facet.values.join(", "))
        end
    end
end
```
