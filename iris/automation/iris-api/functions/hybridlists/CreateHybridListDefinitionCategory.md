---
layout: default
title: CreateHybridListDefinitionCategory
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateHybridListDefinitionCategory

Creates a new category within a hybrid list definition.

## Syntax

```jyro
CreateHybridListDefinitionCategory(definitionId, name, description)
```

## Parameters

- **definitionId** (string): The hybrid list definition ID (GUID)
- **name** (string): Category name
- **description** (string): Category description

## Returns

- **string**: The created category ID (GUID), or `null` if creation failed

## Description

Creates a new category within an existing hybrid list definition. Categories are used to group related items within a hybrid list. For example, a "Countries" hybrid list might have categories like "Europe", "Asia", "Americas", etc.

## Examples

```jyro
# Create region categories for a countries list
var europeId = CreateHybridListDefinitionCategory(
    hybridListDefId,
    "Europe",
    "European countries"
)

var asiaId = CreateHybridListDefinitionCategory(
    hybridListDefId,
    "Asia",
    "Asian countries"
)

var americasId = CreateHybridListDefinitionCategory(
    hybridListDefId,
    "Americas",
    "North and South American countries"
)

Log("Information", "Created Europe category: " + europeId)
Log("Information", "Created Asia category: " + asiaId)
Log("Information", "Created Americas category: " + americasId)
```

```jyro
# Create status categories
var statusListId = CreateHybridListDefinition("Task Statuses", "Status options for tasks")

var activeId = CreateHybridListDefinitionCategory(
    statusListId,
    "Active Statuses",
    "Statuses for tasks in progress"
)

var closedId = CreateHybridListDefinitionCategory(
    statusListId,
    "Closed Statuses",
    "Terminal statuses for completed tasks"
)

# Now add items to each category
CreateHybridListDefinitionItem(statusListId, activeId, "To Do", "todo", null)
CreateHybridListDefinitionItem(statusListId, activeId, "In Progress", "in-progress", null)
CreateHybridListDefinitionItem(statusListId, closedId, "Done", "done", null)
CreateHybridListDefinitionItem(statusListId, closedId, "Cancelled", "cancelled", null)
```

```jyro
# Create categories dynamically from data
var regions = ["North", "South", "East", "West"]
var categoryIds = {}

foreach region in regions do
    var catId = CreateHybridListDefinitionCategory(
        hybridListDefId,
        region + " Region",
        "Locations in the " + region + " region"
    )
    categoryIds[region] = catId
    Log("Information", "Created " + region + " category: " + catId)
end
```

## Error Handling

```jyro
var categoryId = CreateHybridListDefinitionCategory(definitionId, "New Category", "Description")

if categoryId == null then
    Log("Error", "Failed to create category - definition may not exist or name may be duplicate")
else
    Log("Information", "Category created successfully: " + categoryId)
end
```

## Related Functions

- [CreateHybridListDefinition](CreateHybridListDefinition) - Create the parent definition
- [CreateHybridListDefinitionItem](CreateHybridListDefinitionItem) - Add items to categories
- [GetAllHybridListDefinitions](GetAllHybridListDefinitions) - Get all definitions with categories
