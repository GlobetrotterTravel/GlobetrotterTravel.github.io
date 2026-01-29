---
layout: default
title: GetHybridListDefinitionByName
parent: Iris API functions
has_children: false
has_toc: false
---

# GetHybridListDefinitionByName

Retrieves a hybrid list definition by its name.

## Syntax

```jyro
GetHybridListDefinitionByName(name)
```

## Parameters

- **name** (string): The name of the hybrid list definition to find

## Returns

- **object**: The hybrid list definition object with categories and items, or null if not found

## Description

Searches for a hybrid list definition by its exact name. This is a convenience function that avoids the need to iterate through all definitions to find a specific one. The returned object includes the full hierarchy with categories and their items.

This function is commonly used as the first step before working with hybrid list instances, as you need the definition ID to create, read, or modify instance items.

## Examples

```jyro
# Find a hybrid list definition by name
var vendorsDef = GetHybridListDefinitionByName("Preferred Vendors")

if vendorsDef != null then
    Log("Information", "Found definition with ID: " + vendorsDef.id)
    Log("Information", "Categories: " + vendorsDef.categories.length)
else
    Log("Warning", "Preferred Vendors definition not found")
end
```

```jyro
# Use definition to work with instances
var productsDef = GetHybridListDefinitionByName("Product Catalog")

if productsDef != null then
    # Get the instance for this organization
    var instance = GetHybridListInstance(Data.organization.id, productsDef.id)

    if instance != null then
        Log("Information", "Organization has " + instance.categories.length + " product categories")
    end
end
```

```jyro
# Find a category within a definition
var regionsDef = GetHybridListDefinitionByName("Regions")

if regionsDef != null then
    foreach category in regionsDef.categories do
        if category.name == "North America" then
            Log("Information", "Found North America category: " + category.id)
        end
    end
end
```

```jyro
# Check if definition exists before creating items
var servicesDef = GetHybridListDefinitionByName("Service Types")

if servicesDef == null then
    Log("Error", "Service Types definition must be created before running this script")
else
    var firstCategory = servicesDef.categories[0]

    if firstCategory != null then
        CreateHybridListInstanceItem(
            Data.organization.id,
            servicesDef.id,
            firstCategory.id,
            "Custom Service",
            "custom-svc",
            "Added automatically"
        )
    end
end
```

## Returned Object Structure

### Hybrid List Definition

| Property | Type | Description |
|----------|------|-------------|
| id | string | Definition ID (GUID) |
| name | string | List name |
| description | string | List description |
| isActive | boolean | Whether the list is active |
| categories | array | Array of category objects |

### Category

| Property | Type | Description |
|----------|------|-------------|
| id | string | Category ID (GUID) |
| name | string | Category name |
| description | string | Category description |
| items | array | Array of item objects |

### Item

| Property | Type | Description |
|----------|------|-------------|
| id | string | Item ID (GUID) |
| name | string | Item display name |
| value | string | Item value |
| description | string | Item description |

## Notes

- The name match is case-insensitive
- Returns null if no definition with the specified name exists
- Returns null if there is an error fetching the definition
- Only returns active definitions
