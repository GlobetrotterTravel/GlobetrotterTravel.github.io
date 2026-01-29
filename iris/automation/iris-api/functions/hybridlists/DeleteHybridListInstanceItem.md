---
layout: default
title: DeleteHybridListInstanceItem
parent: Iris API functions
has_children: false
has_toc: false
---

# DeleteHybridListInstanceItem

Deletes a custom item from a hybrid list instance.

## Syntax

```jyro
DeleteHybridListInstanceItem(organizationId, hybridListDefinitionId, categoryId, itemId)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **hybridListDefinitionId** (string): The GUID of the hybrid list definition
- **categoryId** (string): The GUID of the category (from the definition)
- **itemId** (string): The GUID of the item to delete

## Returns

- **boolean**: true if the deletion was successful, false otherwise

## Description

Deletes a custom item from a hybrid list instance. Only custom items (items created through `CreateHybridListInstanceItem` or the UI) can be deleted. Definition items are read-only and cannot be removed through this function.

This operation is permanent and cannot be undone.

## Examples

```jyro
# Delete a specific custom item
var vendorsDef = GetHybridListDefinitionByName("Preferred Vendors")
var instance = GetHybridListInstance(Data.organization.id, vendorsDef.id)

if instance != null then
    foreach category in instance.categories do
        foreach item in category.items do
            if item.isCustom and item.value == "deprecated-vendor" then
                var deleted = DeleteHybridListInstanceItem(
                    Data.organization.id,
                    vendorsDef.id,
                    category.hybridListDefinitionCategoryId,
                    item.id
                )

                if deleted then
                    Log("Information", "Successfully deleted vendor: " + item.name)
                else
                    Log("Warning", "Failed to delete vendor: " + item.name)
                end
            end
        end
    end
end
```

```jyro
# Clean up all custom items in a category
var productsDef = GetHybridListDefinitionByName("Products")
var instance = GetHybridListInstance(Data.organization.id, productsDef.id)

if instance != null then
    var tempCategory = null
    foreach cat in instance.categories do
        if cat.name == "Temporary" then
            tempCategory = cat
        end
    end

    if tempCategory != null then
        # Collect items to delete (avoid modifying while iterating)
        var itemsToDelete = []
        foreach item in tempCategory.items do
            if item.isCustom then
                itemsToDelete.add({
                    categoryId: tempCategory.hybridListDefinitionCategoryId,
                    itemId: item.id,
                    name: item.name
                })
            end
        end

        # Delete collected items
        foreach itemInfo in itemsToDelete do
            DeleteHybridListInstanceItem(
                Data.organization.id,
                productsDef.id,
                itemInfo.categoryId,
                itemInfo.itemId
            )
            Log("Information", "Deleted temporary item: " + itemInfo.name)
        end

        Log("Information", "Deleted " + itemsToDelete.length + " temporary items")
    end
end
```

```jyro
# Attempt to delete a definition item (will fail)
var regionsDef = GetHybridListDefinitionByName("Regions")
var instance = GetHybridListInstance(Data.organization.id, regionsDef.id)

if instance != null then
    var firstCategory = instance.categories[0]

    foreach item in firstCategory.items do
        if !item.isCustom then
            var result = DeleteHybridListInstanceItem(
                Data.organization.id,
                regionsDef.id,
                firstCategory.hybridListDefinitionCategoryId,
                item.id
            )

            if !result then
                Log("Information", "Cannot delete definition item: " + item.name)
            end
        end
    end
end
```

```jyro
# Delete items based on condition
var featuresDef = GetHybridListDefinitionByName("Features")
var instance = GetHybridListInstance(Data.organization.id, featuresDef.id)

# Check if organization is being downgraded
var tierFacetId = GetFacetDefinitionByName("Service Tier")
var tierFacet = GetOrganizationFacetInstance(Data.organization.id, tierFacetId)

if tierFacet != null and tierFacet.values[0] == "Basic" then
    # Remove premium features
    if instance != null then
        foreach category in instance.categories do
            if category.name == "Premium" then
                foreach item in category.items do
                    if item.isCustom then
                        DeleteHybridListInstanceItem(
                            Data.organization.id,
                            featuresDef.id,
                            category.hybridListDefinitionCategoryId,
                            item.id
                        )
                        Log("Information", "Removed premium feature: " + item.name)
                    end
                end
            end
        end
    end
end
```

```jyro
# Delete item and recreate with new values (instead of update)
var servicesDef = GetHybridListDefinitionByName("Services")
var instance = GetHybridListInstance(Data.organization.id, servicesDef.id)

if instance != null then
    foreach category in instance.categories do
        foreach item in category.items do
            if item.isCustom and item.value == "old-service" then
                # Delete old item
                var deleted = DeleteHybridListInstanceItem(
                    Data.organization.id,
                    servicesDef.id,
                    category.hybridListDefinitionCategoryId,
                    item.id
                )

                if deleted then
                    # Create replacement with new value
                    CreateHybridListInstanceItem(
                        Data.organization.id,
                        servicesDef.id,
                        category.hybridListDefinitionCategoryId,
                        "New Service",
                        "new-service",
                        "Replaced old-service"
                    )
                    Log("Information", "Replaced old-service with new-service")
                end
            end
        end
    end
end
```

## Notes

- Returns false if the item is a definition item (definition items cannot be deleted)
- Returns false if any of the IDs (organization, definition, category, item) are invalid
- Only custom items (`isCustom = true`) can be deleted
- The deletion is permanent and cannot be undone
- The item must belong to the specified category and definition
- Use `GetHybridListInstance` or `GetHybridListInstanceItems` to find items before deleting
