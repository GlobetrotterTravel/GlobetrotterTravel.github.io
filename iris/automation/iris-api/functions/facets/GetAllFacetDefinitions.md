---
layout: default
title: GetAllFacetDefinitions
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllFacetDefinitions

Retrieves all facet definitions in the tenant.

## Syntax

```jyro
GetAllFacetDefinitions()
```

## Parameters

None

## Returns

- **array**: Array of all facet definition objects

## Description

Returns an array containing all facet definitions in the tenant. Each element contains properties like id, name, category, allowFreeText, allowMultipleValues, values, and isActive.

## Examples

```jyro
# List all facet definitions
var allFacetDefs = GetAllFacetDefinitions()
Log("Information", "Found " + Length(allFacetDefs) + " facet definitions")

foreach facetDef in allFacetDefs do
    Log("Information", "Facet: " + facetDef.name + " (Category: " + facetDef.category + ")")
end
```

```jyro
# Filter organization-specific facets
var allFacetDefs = GetAllFacetDefinitions()
var orgFacets = []

foreach facetDef in allFacetDefs do
    if facetDef.category == "Organization" then
        orgFacets.add(facetDef)
    end
end

Log("Information", "Organization facets: " + Length(orgFacets))
```

```jyro
# Build facet name lookup map
var allFacetDefs = GetAllFacetDefinitions()
var facetNameMap = {}

foreach facetDef in allFacetDefs do
    facetNameMap[facetDef.name] = facetDef.id
end

# Use the map to set specific facets
if facetNameMap["Primary Domain"] != null then
    SetOrganizationFacetInstance(
        Data.Organization.Id.Value,
        facetNameMap["Primary Domain"],
        ["example.com"]
    )
end
```

```jyro
# Auto-populate critical facets
var allFacetDefs = GetAllFacetDefinitions()

foreach facetDef in allFacetDefs do
    if facetDef.category == "Organization" and facetDef.isCritical and facetDef.isActive then
        var defaultValue = []
        if Length(facetDef.values) > 0 then
            defaultValue = [facetDef.values[0]]
        else if facetDef.allowFreeText then
            defaultValue = ["To be determined"]
        end

        if Length(defaultValue) > 0 then
            SetOrganizationFacetInstance(Data.Organization.Id.Value, facetDef.id, defaultValue)
            Log("Information", "Auto-populated facet: " + facetDef.name)
        end
    end
end
```
