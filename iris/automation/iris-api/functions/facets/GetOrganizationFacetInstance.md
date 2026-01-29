---
layout: default
title: GetOrganizationFacetInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# GetOrganizationFacetInstance

Retrieves a single facet instance from an organization by facet definition ID.

## Syntax

```jyro
GetOrganizationFacetInstance(organizationId, facetDefinitionId)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **facetDefinitionId** (string): The GUID of the facet definition

## Returns

- **object**: Facet instance object with { facetDefinitionId, values }, or null if not found

## Description

Retrieves the facet instance for a specific facet definition on an organization. Returns null if the organization doesn't have an instance of that facet.

## Examples

```jyro
# Get single facet instance
var domainFacet = GetFacetDefinitionByName("Primary Domain")
var domain = GetOrganizationFacetInstance(Data.Organization.Id.Value, domainFacet.id)

if domain != null then
    Log("Information", "Primary domain: " + domain.values[0])
end
```

```jyro
# Check if facet is set
var industryFacet = GetFacetDefinitionByName("Industry")
var industry = GetOrganizationFacetInstance(orgId, industryFacet.id)

if industry == null then
    Log("Warning", "Industry not set for organization")
    # Set default
    SetOrganizationFacetInstance(orgId, industryFacet.id, ["Unknown"])
end
```

```jyro
# Access multiple values
var tagsFacet = GetFacetDefinitionByName("Tags")
var tags = GetOrganizationFacetInstance(orgId, tagsFacet.id)

if tags != null then
    Log("Information", "Organization has " + Length(tags.values) + " tags")
    foreach tag in tags.values do
        Log("Information", "  - " + tag)
    end
end
```
