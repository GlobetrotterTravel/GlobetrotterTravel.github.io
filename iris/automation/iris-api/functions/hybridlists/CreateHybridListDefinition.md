---
layout: default
title: CreateHybridListDefinition
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateHybridListDefinition

Creates a new hybrid list definition.

## Syntax

```jyro
CreateHybridListDefinition(name, description)
```

## Parameters

- **name** (string): Hybrid list name (max 100 chars)
- **description** (string): Description (max 500 chars)

## Returns

- **string**: The created hybrid list definition ID (GUID), or `null` if creation failed

## Description

Creates a new hybrid list definition which serves as a container for categorized items. Hybrid lists are hierarchical data structures used for organizing selectable options. After creating a definition, you can add categories with `CreateHybridListDefinitionCategory` and items with `CreateHybridListDefinitionItem`.

## Examples

```jyro
# Create a countries hybrid list
var hybridListDefId = CreateHybridListDefinition(
    "Countries",
    "List of countries organized by region"
)

if hybridListDefId != null then
    Log("Information", "Created Countries list: " + hybridListDefId)

    # Now add categories
    var europeId = CreateHybridListDefinitionCategory(
        hybridListDefId,
        "Europe",
        "European countries"
    )

    # Add items to the category
    CreateHybridListDefinitionItem(hybridListDefId, europeId, "United Kingdom", "GB", "UK")
    CreateHybridListDefinitionItem(hybridListDefId, europeId, "France", "FR", "France")
    CreateHybridListDefinitionItem(hybridListDefId, europeId, "Germany", "DE", "Germany")
end
```

```jyro
# Create a project status list
var statusListId = CreateHybridListDefinition(
    "Project Statuses",
    "Available statuses for project tracking"
)

if statusListId != null then
    var activeId = CreateHybridListDefinitionCategory(statusListId, "Active", "In-progress statuses")
    var closedId = CreateHybridListDefinitionCategory(statusListId, "Closed", "Terminal statuses")

    # Active statuses
    CreateHybridListDefinitionItem(statusListId, activeId, "Not Started", "not-started", null)
    CreateHybridListDefinitionItem(statusListId, activeId, "In Progress", "in-progress", null)
    CreateHybridListDefinitionItem(statusListId, activeId, "On Hold", "on-hold", null)

    # Closed statuses
    CreateHybridListDefinitionItem(statusListId, closedId, "Completed", "completed", null)
    CreateHybridListDefinitionItem(statusListId, closedId, "Cancelled", "cancelled", null)
end
```

```jyro
# Create department hierarchy
var deptListId = CreateHybridListDefinition(
    "Departments",
    "Company organizational structure"
)

var engineeringId = CreateHybridListDefinitionCategory(deptListId, "Engineering", "Technical teams")
CreateHybridListDefinitionItem(deptListId, engineeringId, "Backend", "backend", null)
CreateHybridListDefinitionItem(deptListId, engineeringId, "Frontend", "frontend", null)
CreateHybridListDefinitionItem(deptListId, engineeringId, "DevOps", "devops", null)

var businessId = CreateHybridListDefinitionCategory(deptListId, "Business", "Business teams")
CreateHybridListDefinitionItem(deptListId, businessId, "Sales", "sales", null)
CreateHybridListDefinitionItem(deptListId, businessId, "Marketing", "marketing", null)
```

## Typical Workflow

1. **Create Definition** - `CreateHybridListDefinition(name, description)`
2. **Add Categories** - `CreateHybridListDefinitionCategory(defId, name, description)`
3. **Add Items** - `CreateHybridListDefinitionItem(defId, catId, name, value, description)`
4. **Use in Facets** - Reference the definition in facet definitions

## Related Functions

- [CreateHybridListDefinitionCategory](CreateHybridListDefinitionCategory.md) - Add categories
- [CreateHybridListDefinitionItem](CreateHybridListDefinitionItem.md) - Add items
- [GetAllHybridListDefinitions](GetAllHybridListDefinitions.md) - Get all definitions
- [SetOrganizationHybridListInstance](SetOrganizationHybridListInstance.md) - Set values on organization
