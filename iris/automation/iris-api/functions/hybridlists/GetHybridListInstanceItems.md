---
layout: default
title: GetHybridListInstanceItems
parent: Iris API functions
has_children: false
has_toc: false
---

# GetHybridListInstanceItems

Retrieves all items for a specific category in a hybrid list instance.

## Syntax

```jyro
GetHybridListInstanceItems(organizationId, hybridListDefinitionId, categoryId)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **hybridListDefinitionId** (string): The GUID of the hybrid list definition
- **categoryId** (string): The GUID of the category (from the definition)

## Returns

- **array**: Array of item objects for the specified category, or empty array if none found

## Description

Fetches all items (both definition items and custom items) for a specific category within a hybrid list instance. This function is useful when you only need to work with items from a single category rather than loading the entire instance.

The returned items include both read-only definition items and modifiable custom items, distinguished by the `isCustom` property.

## Examples

```jyro
# Get items for a specific category
var vendorsDef = GetHybridListDefinitionByName("Preferred Vendors")

if vendorsDef != null then
    # Find the "Software Vendors" category
    var softwareCategory = null
    foreach cat in vendorsDef.categories do
        if cat.name == "Software Vendors" then
            softwareCategory = cat
        end
    end

    if softwareCategory != null then
        var items = GetHybridListInstanceItems(
            Data.organization.id,
            vendorsDef.id,
            softwareCategory.id
        )

        Log("Information", "Found " + items.length + " software vendors")

        foreach item in items do
            Log("Information", "  - " + item.name + " (" + item.value + ")")
        end
    end
end
```

```jyro
# Check if an item exists before adding
var productsDef = GetHybridListDefinitionByName("Products")
var electronicsCat = productsDef.categories.filter(c => c.name == "Electronics")[0]

if electronicsCat != null then
    var existingItems = GetHybridListInstanceItems(
        Data.organization.id,
        productsDef.id,
        electronicsCat.id
    )

    var hasLaptops = false
    foreach item in existingItems do
        if item.value == "laptops" then
            hasLaptops = true
        end
    end

    if !hasLaptops then
        CreateHybridListInstanceItem(
            Data.organization.id,
            productsDef.id,
            electronicsCat.id,
            "Laptops",
            "laptops",
            "Portable computers"
        )
        Log("Information", "Added Laptops to Electronics category")
    end
end
```

```jyro
# List only custom items in a category
var regionsDef = GetHybridListDefinitionByName("Regions")
var europeCat = regionsDef.categories.filter(c => c.name == "Europe")[0]

if europeCat != null then
    var items = GetHybridListInstanceItems(
        Data.organization.id,
        regionsDef.id,
        europeCat.id
    )

    Log("Information", "Custom countries in Europe:")
    foreach item in items do
        if item.isCustom then
            Log("Information", "  - " + item.name + " (custom)")
        end
    end
end
```

```jyro
# Build a value lookup for a category
var statusDef = GetHybridListDefinitionByName("Status Codes")
var firstCat = statusDef.categories[0]

var items = GetHybridListInstanceItems(
    Data.organization.id,
    statusDef.id,
    firstCat.id
)

var valueLookup = {}
foreach item in items do
    valueLookup[item.value] = item.name
end

# Now use the lookup
var currentStatus = "active"
Log("Information", "Current status: " + valueLookup[currentStatus])
```

## Returned Object Structure

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

## Notes

- Returns an empty array if no items exist for the specified category
- Returns an empty array if the category ID is invalid
- Both definition items (`isCustom = false`) and custom items (`isCustom = true`) are returned
- Custom items can be updated or deleted; definition items are read-only
- Use the category ID from the definition, not from the instance
