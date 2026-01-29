---
layout: default
title: CreateHybridListDefinitionItem
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateHybridListDefinitionItem

Creates a new item within a hybrid list definition category.

## Syntax

```jyro
CreateHybridListDefinitionItem(definitionId, categoryId, name, value)
CreateHybridListDefinitionItem(definitionId, categoryId, name, value, description)
```

## Parameters

- **definitionId** (string): The hybrid list definition ID (GUID)
- **categoryId** (string): The category ID (GUID)
- **name** (string): Item display name
- **value** (string): Item value (typically used as the stored/selected value)
- **description** (string, optional): Item description

## Returns

- **string**: The created item ID (GUID), or `null` if creation failed

## Description

Creates a new item within a category of a hybrid list definition. Items are the selectable values that users can choose from. Each item has a display name (shown to users) and a value (stored when selected).

## Examples

```jyro
# Create country items
var itemId = CreateHybridListDefinitionItem(
    hybridListDefId,
    europeCategoryId,
    "United Kingdom",
    "GB",
    "United Kingdom of Great Britain and Northern Ireland"
)

CreateHybridListDefinitionItem(hybridListDefId, europeCategoryId, "France", "FR", "French Republic")
CreateHybridListDefinitionItem(hybridListDefId, europeCategoryId, "Germany", "DE", "Federal Republic of Germany")
CreateHybridListDefinitionItem(hybridListDefId, europeCategoryId, "Spain", "ES", "Kingdom of Spain")
```

```jyro
# Create status items without descriptions
CreateHybridListDefinitionItem(statusListId, activeCategoryId, "Not Started", "not-started")
CreateHybridListDefinitionItem(statusListId, activeCategoryId, "In Progress", "in-progress")
CreateHybridListDefinitionItem(statusListId, activeCategoryId, "On Hold", "on-hold")
CreateHybridListDefinitionItem(statusListId, closedCategoryId, "Completed", "completed")
CreateHybridListDefinitionItem(statusListId, closedCategoryId, "Cancelled", "cancelled")
```

```jyro
# Create items from an array
var countries = [
    { "name": "United States", "code": "US", "desc": "United States of America" },
    { "name": "Canada", "code": "CA", "desc": "Canada" },
    { "name": "Mexico", "code": "MX", "desc": "United Mexican States" }
]

foreach country in countries do
    var itemId = CreateHybridListDefinitionItem(
        hybridListDefId,
        americasCategoryId,
        country.name,
        country.code,
        country.desc
    )
    Log("Information", "Created " + country.name + ": " + itemId)
end
```

```jyro
# Complete workflow: create list, category, and items
var listId = CreateHybridListDefinition("Priority Levels", "Task priority options")

if listId != null then
    var categoryId = CreateHybridListDefinitionCategory(listId, "All Priorities", "Available priority levels")

    if categoryId != null then
        CreateHybridListDefinitionItem(listId, categoryId, "Critical", "P1", "Immediate attention required")
        CreateHybridListDefinitionItem(listId, categoryId, "High", "P2", "Should be addressed soon")
        CreateHybridListDefinitionItem(listId, categoryId, "Medium", "P3", "Normal priority")
        CreateHybridListDefinitionItem(listId, categoryId, "Low", "P4", "Can be deferred")

        Log("Information", "Priority list created successfully")
    end
end
```

## Error Handling

```jyro
var itemId = CreateHybridListDefinitionItem(
    definitionId,
    categoryId,
    "New Item",
    "new-item",
    "Description"
)

if itemId == null then
    Log("Error", "Failed to create item - check that definition and category exist")
else
    Log("Information", "Item created: " + itemId)
end
```

## Related Functions

- [CreateHybridListDefinition](CreateHybridListDefinition.md) - Create the parent definition
- [CreateHybridListDefinitionCategory](CreateHybridListDefinitionCategory.md) - Create categories
- [GetAllHybridListDefinitions](GetAllHybridListDefinitions.md) - Get all definitions with items
- [SetOrganizationHybridListInstance](SetOrganizationHybridListInstance.md) - Use items on organizations
