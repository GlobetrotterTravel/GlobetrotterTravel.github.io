---
layout: default
title: GetAllHybridListDefinitions
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllHybridListDefinitions

Retrieves all hybrid list definitions in the current tenant with their categories and items.

## Syntax

```jyro
GetAllHybridListDefinitions()
```

## Parameters

None.

## Returns

- **array**: Array of hybrid list definition objects with full hierarchy (categories and items), or empty array if none found or error

## Description

Fetches all hybrid list definitions configured for the current tenant, including their nested categories and items. Hybrid lists are hierarchical data structures used for organizing selectable options (e.g., countries organized by region, departments organized by division).

## Examples

```jyro
# Get all hybrid list definitions
var hybridListDefs = GetAllHybridListDefinitions()
if hybridListDefs != null and Length(hybridListDefs) > 0 then
    Log("Information", "Found " + Length(hybridListDefs) + " hybrid list definitions")
end
```

```jyro
# Iterate over definitions and their structure
var hybridListDefs = GetAllHybridListDefinitions()
foreach def in hybridListDefs do
    Log("Information", "Hybrid List: " + def.name + " has " + Length(def.categories) + " categories")

    foreach category in def.categories do
        Log("Information", "  Category: " + category.name + " has " + Length(category.items) + " items")

        foreach item in category.items do
            Log("Information", "    Item: " + item.name + " = " + item.value)
        end
    end
end
```

```jyro
# Find a specific hybrid list by name
var hybridListDefs = GetAllHybridListDefinitions()
var countryList = null
foreach def in hybridListDefs do
    if def.name == "Countries" then
        countryList = def
    end
end

if countryList != null then
    # Find Europe category
    foreach category in countryList.categories do
        if category.name == "Europe" then
            Log("Information", "Europe has " + Length(category.items) + " countries")
        end
    end
end
```

```jyro
# Build a flat lookup of all items across all lists
var hybridListDefs = GetAllHybridListDefinitions()
var allItems = []
foreach def in hybridListDefs do
    foreach category in def.categories do
        foreach item in category.items do
            allItems.add({
                "listName": def.name,
                "categoryName": category.name,
                "itemName": item.name,
                "itemValue": item.value
            })
        end
    end
end
Log("Information", "Total items across all lists: " + Length(allItems))
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
