---
layout: default
title: SetOrganizationHybridListInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# SetOrganizationHybridListInstance

Sets or updates hybrid list instance items on an organization.

## Syntax

```jyro
SetOrganizationHybridListInstance(organizationId, hybridListDefinitionId, categories)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **hybridListDefinitionId** (string): The GUID of the hybrid list definition
- **categories** (array): Array of category objects with the following structure:
  - **categoryId** (string): The GUID of the hybrid list definition category (required)
  - **items** (array): Array of item objects (required):
    - **name** (string): The name/label of the item (required)
    - **value** (string): The value of the item (required)
    - **description** (string|null): Optional description for the item

## Returns

- **boolean**: true if successful, false otherwise

## Description

Smart detection: Works in both "Before" hooks (in-memory) and "After" hooks (persisted):
- If organization exists in database: Fetches, modifies, and saves
- If organization is in-memory only: Modifies Data.Organization in execution context

This function replaces all existing hybrid list instance items for the specified definition with the new items provided. Items are organized by category, where each category ID must correspond to a valid category in the hybrid list definition.

The function performs the following steps:
1. Validates the hybrid list definition exists
2. Validates all provided category IDs belong to the definition
3. Removes all existing instance items belonging to any category of this definition
4. Adds new items from the provided categories

## Examples

```jyro
# Get hybrid list definition and set items
var hybridLists = GetAllHybridListDefinitions()
var countryList = hybridLists.filter(h => h.name == "Countries")[0]

if countryList != null then
    var europeCategory = countryList.categories.filter(c => c.name == "Europe")[0]
    var asiaCategory = countryList.categories.filter(c => c.name == "Asia")[0]

    SetOrganizationHybridListInstance(
        Data.Organization.Id.Value,
        countryList.id,
        [
            {
                categoryId: europeCategory.id,
                items: [
                    {name: "France", value: "FR", description: "French Republic"},
                    {name: "Germany", value: "DE", description: "Federal Republic of Germany"},
                    {name: "United Kingdom", value: "GB", description: null}
                ]
            },
            {
                categoryId: asiaCategory.id,
                items: [
                    {name: "Japan", value: "JP", description: "Land of the Rising Sun"},
                    {name: "South Korea", value: "KR", description: null}
                ]
            }
        ]
    )
end
```

```jyro
# Set hybrid list items in BeforeOrganizationCreated hook
var serviceLevels = GetAllHybridListDefinitions().filter(h => h.name == "Service Levels")[0]

if serviceLevels != null and serviceLevels.categories.length > 0 then
    var defaultCategory = serviceLevels.categories[0]

    SetOrganizationHybridListInstance(
        Data.Organization.Id.Value,
        serviceLevels.id,
        [
            {
                categoryId: defaultCategory.id,
                items: [
                    {name: "Standard", value: "STD", description: "Standard support"},
                    {name: "Premium", value: "PRM", description: "Priority support"},
                    {name: "Enterprise", value: "ENT", description: "24/7 dedicated support"}
                ]
            }
        ]
    )
    Log("Information", "Default service levels set for new organization")
end
```

```jyro
# Update hybrid list items in AfterOrganizationUpdated hook
var productCatalog = GetAllHybridListDefinitions().filter(h => h.name == "Products")[0]

if productCatalog != null then
    # Build categories dynamically
    var categoryItems = []

    foreach category in productCatalog.categories do
        # Add default items for each category
        categoryItems.add({
            categoryId: category.id,
            items: [
                {name: "Default Item", value: "DEFAULT", description: "Auto-generated item for " + category.name}
            ]
        })
    end

    var success = SetOrganizationHybridListInstance(
        Data.Current.Id.Value,
        productCatalog.id,
        categoryItems
    )

    if success then
        Log("Information", "Product catalog updated with defaults")
    else
        Log("Warning", "Failed to update product catalog")
    end
end
```

```jyro
# Conditional hybrid list setup based on organization type
var orgType = Data.Organization.FacetInstances.filter(f => f.facetDefinitionId == GetFacetDefinitionByName("Organization Type").id)

if orgType.length > 0 and orgType[0].values[0] == "Enterprise" then
    var features = GetAllHybridListDefinitions().filter(h => h.name == "Features")[0]

    if features != null then
        var premiumCategory = features.categories.filter(c => c.name == "Premium")[0]

        if premiumCategory != null then
            SetOrganizationHybridListInstance(
                Data.Organization.Id.Value,
                features.id,
                [
                    {
                        categoryId: premiumCategory.id,
                        items: [
                            {name: "Advanced Analytics", value: "analytics", description: null},
                            {name: "Custom Integrations", value: "integrations", description: null},
                            {name: "Dedicated Support", value: "support", description: null}
                        ]
                    }
                ]
            )
            Log("Information", "Enterprise features enabled for organization")
        end
    end
end
```

## Notes

- The hybrid list definition must exist in the tenant before calling this function
- All category IDs must belong to the specified hybrid list definition
- Invalid categories or items are skipped with a warning
- If no valid items are provided across all categories, the function returns false
- Calling this function removes ALL existing items for the definition before adding new ones
- Aggregate IDs (Id, TenantId) are protected and cannot be modified by scripts
- Each item gets a new auto-generated ID when created
- Items created through this function do not have a `hybridListDefinitionItemId` reference (they are custom items)
