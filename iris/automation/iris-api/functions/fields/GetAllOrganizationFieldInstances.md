---
layout: default
title: GetAllOrganizationFieldInstances
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllOrganizationFieldInstances

Retrieves all field instances from an organization.

## Syntax

```jyro
GetAllOrganizationFieldInstances(organizationId)
```

## Parameters

- **organizationId** (string): The GUID of the organization

## Returns

- **array**: Array of field instance objects, or empty array if none found

Each object contains:
- **id** (string): The GUID of the field instance
- **fieldDefinitionId** (string): The GUID of the field definition
- **label** (string or null): The label of the field
- **description** (string or null): The description
- **hint** (string or null): The hint shown to consultants
- **order** (number): The display order
- **fieldRequirementLevel** (string): "Required", "Optional", or "Hidden"
- **regexPattern** (string or null): Validation regex
- **allowFreeTextOverride** (boolean): Whether free text is allowed
- **items** (array): Array of field instance items, each with:
  - **id** (string): Item GUID
  - **name** (string): Item name
  - **value** (string): Item value
  - **description** (string or null): Item description

## Description

Returns all field instances currently assigned to an organization. Use this to audit what fields an organization has, check for populated values, or perform batch operations on field data.

## Examples

```jyro
# List all fields for an organization
var fields = GetAllOrganizationFieldInstances(Data.Organization.Id)

if fields.length > 0 then
    Log("Information", "Organization has " + fields.length + " field instances")

    foreach field in fields do
        Log("Information", "Field: " + field.fieldDefinitionId)
        Log("Information", "  Items: " + field.items.length)

        foreach item in field.items do
            Log("Information", "    - " + item.name + " = " + item.value)
        end
    end
else
    Log("Information", "Organization has no field instances")
end
```

```jyro
# Check if organization has a specific field by definition ID
var targetDefId = GetAllFieldDefinitions()[0].id
var fields = GetAllOrganizationFieldInstances(Data.Organization.Id)
var hasField = false

foreach field in fields do
    if field.fieldDefinitionId == targetDefId then
        hasField = true
        Log("Information", "Found field with " + field.items.length + " items")
        break
    end
end

if not hasField then
    Log("Warning", "Organization missing expected field")
end
```

```jyro
# Build a map of field values by definition ID
var fields = GetAllOrganizationFieldInstances(Data.Organization.Id)
var fieldMap = {}

foreach field in fields do
    var values = []
    foreach item in field.items do
        values.add(item.value)
    end
    fieldMap[field.fieldDefinitionId] = values
end

# Now you can access field values by definition ID
Log("Information", "Field values mapped: " + Keys(fieldMap).length + " fields")
```

```jyro
# Count total items across all fields
var fields = GetAllOrganizationFieldInstances(Data.Organization.Id)
var totalItems = 0

foreach field in fields do
    totalItems = totalItems + field.items.length
end

Log("Information", "Organization has " + totalItems + " field items across " + fields.length + " fields")
```

```jyro
# Find required fields that are empty
var fields = GetAllOrganizationFieldInstances(Data.Organization.Id)
var emptyRequiredFields = []

foreach field in fields do
    if field.fieldRequirementLevel == "Required" and field.items.length == 0 then
        emptyRequiredFields.add(field)
    end
end

if emptyRequiredFields.length > 0 then
    Log("Warning", emptyRequiredFields.length + " required fields are empty")
end
```

## See Also

- [GetOrganizationFieldInstanceByName](GetOrganizationFieldInstanceByName) - Get single field by name
- [SetOrganizationFieldInstance](SetOrganizationFieldInstance) - Set field items
- [GetAllFieldDefinitions](GetAllFieldDefinitions) - Get all field definitions
