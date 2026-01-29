---
layout: default
title: GetHybridListInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# GetHybridListInstance

Retrieves a specific hybrid list instance by organization and definition ID.

## Syntax

```jyro
GetHybridListInstance(organizationId, hybridListDefinitionId)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **hybridListDefinitionId** (string): The GUID of the hybrid list definition

## Returns

- **object**: The hybrid list instance object with categories, items, and facet instances, or null if not found

## Description

Fetches a specific hybrid list instance for an organization based on the hybrid list definition ID. This function returns the complete merged view of the instance, including both definition items and custom items.

Use this function when you need to work with a specific hybrid list for an organization, rather than fetching all instances.

## Examples

```jyro
# Get a specific hybrid list instance
var defId = GetHybridListDefinitionByName("Preferred Vendors")

if defId != null then
    var instance = GetHybridListInstance(Data.organization.id, defId.id)

    if instance != null then
        Log("Information", "Found instance: " + instance.name)
        Log("Information", "Categories: " + instance.categories.length)
    else
        Log("Warning", "No vendor instance found for this organization")
    end
end
```

```jyro
# Iterate through all items in an instance
var productsDef = GetHybridListDefinitionByName("Product Catalog")
var instance = GetHybridListInstance(Data.organization.id, productsDef.id)

if instance != null then
    foreach category in instance.categories do
        Log("Information", "Category: " + category.name)

        foreach item in category.items do
            Log("Information", "  - " + item.name + " (" + item.value + ")")
        end
    end
end
```

```jyro
# Count custom vs definition items
var regionsDef = GetHybridListDefinitionByName("Regions")
var instance = GetHybridListInstance(Data.current.id, regionsDef.id)

if instance != null then
    var customCount = 0
    var definitionCount = 0

    foreach category in instance.categories do
        foreach item in category.items do
            if item.isCustom then
                customCount = customCount + 1
            else
                definitionCount = definitionCount + 1
            end
        end
    end

    Log("Information", "Definition items: " + definitionCount)
    Log("Information", "Custom items: " + customCount)
end
```

```jyro
# Check for specific item in instance
var servicesDef = GetHybridListDefinitionByName("Service Types")
var instance = GetHybridListInstance(Data.organization.id, servicesDef.id)

var hasPremiumService = false
if instance != null then
    foreach category in instance.categories do
        foreach item in category.items do
            if item.value == "premium" then
                hasPremiumService = true
            end
        end
    end
end

if hasPremiumService then
    Log("Information", "Organization has premium service enabled")
else
    Log("Information", "Organization does not have premium service")
end
```

## Returned Object Structure

### Hybrid List Instance

| Property | Type | Description |
|----------|------|-------------|
| id | string | Instance ID (GUID) |
| hybridListDefinitionId | string | Parent definition ID (GUID) |
| name | string | Instance name (inherited from definition) |
| description | string | Instance description |
| categories | array | Array of category instance objects |
| facetInstances | array | Array of facet instance objects |

### Category Instance

| Property | Type | Description |
|----------|------|-------------|
| id | string | Category instance ID (GUID) |
| hybridListDefinitionCategoryId | string | Parent definition category ID (GUID) |
| name | string | Category name |
| description | string | Category description |
| items | array | Array of item objects |

### Item

| Property | Type | Description |
|----------|------|-------------|
| id | string | Item ID (GUID) |
| hybridListDefinitionCategoryId | string | Parent category ID (GUID) |
| hybridListDefinitionItemId | string | Definition item ID (GUID), null for custom items |
| name | string | Item display name |
| value | string | Item value |
| description | string | Item description |
| isCustom | boolean | True if this is an organization-specific custom item |

### Facet Instance

| Property | Type | Description |
|----------|------|-------------|
| id | string | Facet instance ID (GUID) |
| facetDefinitionId | string | Parent facet definition ID (GUID) |
| name | string | Facet name |
| values | array | Array of facet values (strings) |

## Notes

- Returns null if the organization does not have an instance for the specified definition
- Returns null if either the organization ID or definition ID is invalid
- Use `GetHybridListDefinitionByName()` to get the definition ID by name
- The instance may not exist if the organization hasn't been assigned this hybrid list yet
