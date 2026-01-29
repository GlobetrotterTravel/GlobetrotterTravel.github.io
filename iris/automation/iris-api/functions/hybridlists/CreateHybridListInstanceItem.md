---
layout: default
title: CreateHybridListInstanceItem
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateHybridListInstanceItem

Creates a custom item in a hybrid list instance category.

## Syntax

```jyro
CreateHybridListInstanceItem(organizationId, hybridListDefinitionId, categoryId, name, value, description)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **hybridListDefinitionId** (string): The GUID of the hybrid list definition
- **categoryId** (string): The GUID of the category (from the definition)
- **name** (string): The display name for the item (required, cannot be empty)
- **value** (string): The value/code for the item (required, cannot be empty)
- **description** (string|null): Optional description for the item

## Returns

- **string**: The ID (GUID) of the created item, or null on error

## Description

Creates a new custom item in a specific category of a hybrid list instance. Custom items are organization-specific and can be modified or deleted later, unlike definition items which are read-only.

This function is commonly used to add organization-specific values to a hybrid list that supplements the tenant-wide definition items.

## Examples

```jyro
# Create a custom vendor item
var vendorsDef = GetHybridListDefinitionByName("Preferred Vendors")

if vendorsDef != null then
    var softwareCat = vendorsDef.categories.filter(c => c.name == "Software Vendors")[0]

    if softwareCat != null then
        var newItemId = CreateHybridListInstanceItem(
            Data.organization.id,
            vendorsDef.id,
            softwareCat.id,
            "Acme Software",
            "acme-123",
            "Our primary software vendor"
        )

        if newItemId != null then
            Log("Information", "Created vendor with ID: " + newItemId)
        else
            Log("Error", "Failed to create vendor")
        end
    end
end
```

```jyro
# Add custom item without description
var productsDef = GetHybridListDefinitionByName("Products")
var miscCat = productsDef.categories.filter(c => c.name == "Miscellaneous")[0]

CreateHybridListInstanceItem(
    Data.organization.id,
    productsDef.id,
    miscCat.id,
    "Custom Widget",
    "WIDGET-001",
    null  # No description
)
```

```jyro
# Add multiple items to a category
var regionsDef = GetHybridListDefinitionByName("Regions")
var customRegionCat = regionsDef.categories.filter(c => c.name == "Custom Regions")[0]

if customRegionCat != null then
    var newRegions = [
        {name: "Pacific Northwest", value: "PNW", description: "WA, OR, ID"},
        {name: "Southwest", value: "SW", description: "AZ, NM, NV"},
        {name: "Mountain", value: "MTN", description: "CO, UT, WY, MT"}
    ]

    foreach region in newRegions do
        var itemId = CreateHybridListInstanceItem(
            Data.organization.id,
            regionsDef.id,
            customRegionCat.id,
            region.name,
            region.value,
            region.description
        )

        if itemId != null then
            Log("Information", "Created region: " + region.name)
        end
    end
end
```

```jyro
# Conditionally add item based on organization type
var orgTypeFacetId = GetFacetDefinitionByName("Organization Type")
var orgTypeFacet = GetOrganizationFacetInstance(Data.organization.id, orgTypeFacetId)

if orgTypeFacet != null and orgTypeFacet.values[0] == "Enterprise" then
    var featuresDef = GetHybridListDefinitionByName("Features")
    var premiumCat = featuresDef.categories.filter(c => c.name == "Premium")[0]

    if premiumCat != null then
        CreateHybridListInstanceItem(
            Data.organization.id,
            featuresDef.id,
            premiumCat.id,
            "Dedicated Support",
            "dedicated-support",
            "24/7 dedicated support line"
        )
        Log("Information", "Added premium feature for enterprise organization")
    end
end
```

```jyro
# Create item in BeforeOrganizationCreated hook
var serviceLevelsDef = GetHybridListDefinitionByName("Service Levels")

if serviceLevelsDef != null then
    var defaultCat = serviceLevelsDef.categories[0]

    CreateHybridListInstanceItem(
        Data.organization.id,
        serviceLevelsDef.id,
        defaultCat.id,
        "Standard",
        "standard",
        "Default service level for new organizations"
    )
end
```

## Notes

- Returns null if the organization, definition, or category ID is invalid
- Returns null if name or value is empty or whitespace
- The created item will have `isCustom = true`
- The item's `hybridListDefinitionItemId` will be null (indicating it's a custom item)
- Custom items can be updated with `UpdateHybridListInstanceItem` or deleted with `DeleteHybridListInstanceItem`
- Definition items cannot be created through this function - they must be added to the hybrid list definition
- The category ID must belong to the specified hybrid list definition
