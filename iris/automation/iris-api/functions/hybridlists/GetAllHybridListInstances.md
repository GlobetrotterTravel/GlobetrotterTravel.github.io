---
layout: default
title: GetAllHybridListInstances
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllHybridListInstances

Retrieves all hybrid list instances for an organization.

## Syntax

```jyro
GetAllHybridListInstances(organizationId)
```

## Parameters

- **organizationId** (string): The GUID of the organization

## Returns

- **array**: Array of hybrid list instance objects with categories, items, and facet instances, or empty array if none found

## Description

Fetches all hybrid list instances configured for a specific organization. Each instance represents the organization's version of a hybrid list definition, containing both definition items (read-only) and custom items (organization-specific).

The returned instances include:
- Merged view of definition items and custom items based on the organization's `HybridListItemPolicy`
- All categories with their items
- Facet instances associated with each hybrid list instance

## Examples

```jyro
# Get all hybrid list instances for the current organization
var instances = GetAllHybridListInstances(Data.organization.id)

if instances != null and instances.length > 0 then
    Log("Information", "Organization has " + instances.length + " hybrid list instances")

    foreach instance in instances do
        Log("Information", "  List: " + instance.name)
    end
else
    Log("Information", "Organization has no hybrid list instances")
end
```

```jyro
# Summarize all hybrid lists for an organization
var instances = GetAllHybridListInstances(Data.current.id)

var totalItems = 0
foreach instance in instances do
    foreach category in instance.categories do
        totalItems = totalItems + category.items.length
    end
end

Log("Information", "Organization has " + totalItems + " total items across all hybrid lists")
```

```jyro
# Find a specific instance by definition name
var instances = GetAllHybridListInstances(Data.organization.id)
var vendorInstance = null

foreach instance in instances do
    if instance.name == "Preferred Vendors" then
        vendorInstance = instance
    end
end

if vendorInstance != null then
    Log("Information", "Found vendor list with " + vendorInstance.categories.length + " categories")
end
```

```jyro
# Build a report of all hybrid list data
var instances = GetAllHybridListInstances(Data.organization.id)

foreach instance in instances do
    Log("Information", "=== " + instance.name + " ===")
    Log("Information", "Description: " + instance.description)

    foreach category in instance.categories do
        Log("Information", "  Category: " + category.name)

        foreach item in category.items do
            var customFlag = ""
            if item.isCustom then
                customFlag = " [CUSTOM]"
            end
            Log("Information", "    - " + item.name + " = " + item.value + customFlag)
        end
    end
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

- Returns an empty array if the organization has no hybrid list instances
- Returns an empty array if there is an error fetching instances
- Items with `isCustom = true` can be modified or deleted; definition items cannot
- The merge behavior depends on the organization's `HybridListItemPolicy` (Replace or Extend)
