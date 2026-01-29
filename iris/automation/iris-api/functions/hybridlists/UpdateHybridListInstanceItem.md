---
layout: default
title: UpdateHybridListInstanceItem
parent: Iris API functions
has_children: false
has_toc: false
---

# UpdateHybridListInstanceItem

Updates an existing custom item in a hybrid list instance.

## Syntax

```jyro
UpdateHybridListInstanceItem(organizationId, hybridListDefinitionId, categoryId, itemId, name, value, description)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **hybridListDefinitionId** (string): The GUID of the hybrid list definition
- **categoryId** (string): The GUID of the category (from the definition)
- **itemId** (string): The GUID of the item to update
- **name** (string): The new display name for the item (required, cannot be empty)
- **value** (string): The new value/code for the item (required, cannot be empty)
- **description** (string|null): The new description for the item (optional)

## Returns

- **boolean**: true if the update was successful, false otherwise

## Description

Updates an existing custom item in a hybrid list instance. Only custom items (items created through `CreateHybridListInstanceItem` or the UI) can be updated. Definition items are read-only and cannot be modified through this function.

All fields (name, value, description) are replaced with the new values provided. To keep the current value of a field, you must pass the existing value.

## Examples

```jyro
# Update a custom vendor item
var vendorsDef = GetHybridListDefinitionByName("Preferred Vendors")
var instance = GetHybridListInstance(Data.organization.id, vendorsDef.id)

if instance != null then
    foreach category in instance.categories do
        foreach item in category.items do
            if item.isCustom and item.value == "acme-123" then
                var updated = UpdateHybridListInstanceItem(
                    Data.organization.id,
                    vendorsDef.id,
                    category.hybridListDefinitionCategoryId,
                    item.id,
                    "Acme Software Inc.",  # Updated name
                    "acme-123",             # Same value
                    "Updated: Our primary software vendor since 2020"
                )

                if updated then
                    Log("Information", "Successfully updated vendor")
                else
                    Log("Warning", "Failed to update vendor")
                end
            end
        end
    end
end
```

```jyro
# Update item value based on external data
var productsDef = GetHybridListDefinitionByName("Products")
var instance = GetHybridListInstance(Data.organization.id, productsDef.id)

if instance != null then
    foreach category in instance.categories do
        foreach item in category.items do
            # Only update custom items with old value format
            if item.isCustom and item.value.startsWith("OLD-") then
                var newValue = item.value.replace("OLD-", "NEW-")

                UpdateHybridListInstanceItem(
                    Data.organization.id,
                    productsDef.id,
                    category.hybridListDefinitionCategoryId,
                    item.id,
                    item.name,
                    newValue,
                    item.description
                )

                Log("Information", "Updated item value: " + item.value + " -> " + newValue)
            end
        end
    end
end
```

```jyro
# Attempt to update a definition item (will fail)
var regionsDef = GetHybridListDefinitionByName("Regions")
var instance = GetHybridListInstance(Data.organization.id, regionsDef.id)

if instance != null then
    var firstCategory = instance.categories[0]

    foreach item in firstCategory.items do
        if !item.isCustom then
            var result = UpdateHybridListInstanceItem(
                Data.organization.id,
                regionsDef.id,
                firstCategory.hybridListDefinitionCategoryId,
                item.id,
                "Modified Name",
                item.value,
                "Attempted modification"
            )

            if !result then
                Log("Information", "Cannot update definition item: " + item.name)
            end
        end
    end
end
```

```jyro
# Update item based on hook context
var servicesDef = GetHybridListDefinitionByName("Services")
var instance = GetHybridListInstance(Data.current.id, servicesDef.id)

if instance != null then
    # Find item that needs updating based on organization change
    foreach category in instance.categories do
        foreach item in category.items do
            if item.isCustom and item.name == "Custom Service" then
                UpdateHybridListInstanceItem(
                    Data.current.id,
                    servicesDef.id,
                    category.hybridListDefinitionCategoryId,
                    item.id,
                    "Custom Service for " + Data.current.name,
                    item.value,
                    "Updated on organization change"
                )
            end
        end
    end
end
```

## Notes

- Returns false if the item is a definition item (definition items are read-only)
- Returns false if any of the IDs (organization, definition, category, item) are invalid
- Returns false if name or value is empty or whitespace
- Only custom items (`isCustom = true`) can be updated
- The item must belong to the specified category and definition
- All fields are replaced - pass existing values for fields you don't want to change
- Use `GetHybridListInstance` or `GetHybridListInstanceItems` to get current item values before updating
