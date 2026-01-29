---
layout: default
title: GetAllFieldDefinitions
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllFieldDefinitions

Retrieves all field definitions in the current tenant.

## Syntax

```jyro
GetAllFieldDefinitions()
```

## Parameters

None.

## Returns

- **array**: Array of field definition objects with { id, name, label, category, description, maxLength, defaultOrder, setDefinitionId, setDefinitionName, isActive }, or empty array if none found or error

## Description

Fetches all field definitions configured for the current tenant. Field definitions define custom fields that can be attached to resources like organizations or persons.

## Examples

```jyro
# Get all field definitions
var fieldDefs = GetAllFieldDefinitions()
if fieldDefs != null and Length(fieldDefs) > 0 then
    Log("Information", "Found " + Length(fieldDefs) + " field definitions")
end
```

```jyro
# Find fields by category
var fieldDefs = GetAllFieldDefinitions()
foreach field in fieldDefs do
    if field.category == "Address" then
        Log("Information", "Address field: " + field.name + " (" + field.label + ")")
    end
end
```

```jyro
# Find fields with set definitions (dropdown options)
var fieldDefs = GetAllFieldDefinitions()
foreach field in fieldDefs do
    if field.setDefinitionId != null then
        Log("Information", "Field with options: " + field.name + " -> Set: " + field.setDefinitionName)
    end
end
```

```jyro
# Build a lookup map by name
var fieldDefs = GetAllFieldDefinitions()
var fieldMap = {}
foreach field in fieldDefs do
    fieldMap[field.name] = field.id
end

# Use the map
var emailFieldId = fieldMap["email"]
if emailFieldId != null then
    Log("Information", "Email field ID: " + emailFieldId)
end
```

## Returned Object Properties

| Property | Type | Description |
|----------|------|-------------|
| id | string | Field definition ID (GUID) |
| name | string | Internal name |
| label | string | Display label |
| category | string | Category grouping |
| description | string | Description text |
| maxLength | number | Maximum allowed length |
| defaultOrder | number | Sort order |
| setDefinitionId | string | Optional set definition ID for dropdown values |
| setDefinitionName | string | Optional set definition name |
| isActive | boolean | Whether the field is active |
